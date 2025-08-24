ARG PYTHON_VERSION=3.13
FROM python:${PYTHON_VERSION}-slim-bookworm AS base

# System deps (curl + minimal build tools if wheels aren’t available)
RUN apt-get update \
 && apt-get install -y --no-install-recommends curl ca-certificates \
 && rm -rf /var/lib/apt/lists/*

# --- Pin Poetry version for reproducibility ---
ARG POETRY_VERSION=1.8.3
ENV POETRY_HOME=/opt/poetry \
    POETRY_VERSION=${POETRY_VERSION} \
    POETRY_CACHE_DIR=/root/.cache/pypoetry \
    PIP_CACHE_DIR=/root/.cache/pip \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1
ENV PATH="${POETRY_HOME}/bin:${PATH}"

# Install Poetry (pinned)
RUN curl -sSL https://install.python-poetry.org | python3 - --version ${POETRY_VERSION}

WORKDIR /app

# -------- deps stage: only runs when lock/manifest change --------
FROM base AS deps

# If you prefer a self-contained venv inside the project:
# RUN poetry config virtualenvs.in-project true

# Copy only manifests to maximize cache hits
COPY pyproject.toml poetry.lock ./

# Install dependencies only (no project), no dev groups for runtime images
# Use BuildKit caches for Poetry & pip
RUN --mount=type=cache,target=/root/.cache/pypoetry \
    --mount=type=cache,target=/root/.cache/pip \
    poetry install --no-root --only main --sync --no-ansi

# -------- runtime stage --------
FROM base AS runtime

# If you enabled in-project venv in deps, copy it across:
# COPY --from=deps /app/.venv /app/.venv
# ENV PATH="/app/.venv/bin:${PATH}"

# If you kept Poetry’s default (virtualenvs.create=false), just copy site-packages is messy;
# instead rerun a fast sync against caches (still quick) or keep venv mode above.
COPY --from=deps /root/.cache /root/.cache

# Copy application code last so deps layer stays cached
COPY . /app

# Ensure environment is in sync (no resolution; deps already cached)
RUN --mount=type=cache,target=/root/.cache/pypoetry \
    --mount=type=cache,target=/root/.cache/pip \
    poetry install --no-root --only main --sync --no-ansi

CMD ["python", "main.py"]

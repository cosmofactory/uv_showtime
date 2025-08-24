# syntax=docker/dockerfile:1.7
FROM python:3.12-slim-bookworm

# tiny base: curl for installer
RUN apt-get update \
 && apt-get install -y --no-install-recommends curl \
 && rm -rf /var/lib/apt/lists/*

# install Poetry (pin if you want reproducibility)
ENV POETRY_HOME=/opt/poetry \
    POETRY_NO_INTERACTION=1 \
    POETRY_VIRTUALENVS_IN_PROJECT=true \
    PATH="/opt/poetry/bin:${PATH}"
RUN curl -sSL https://install.python-poetry.org | python3 -

WORKDIR /app

# copy only manifests first → cacheable deps layer
COPY pyproject.toml poetry.lock ./

# fast warm builds via caches; install runtime deps only
RUN --mount=type=cache,target=/root/.cache/pypoetry \
    --mount=type=cache,target=/root/.cache/pip \
    poetry install --no-root --without dev --sync --no-ansi

# now copy the rest of the app (won’t invalidate deps)
COPY . .

CMD ["python", "main.py"]

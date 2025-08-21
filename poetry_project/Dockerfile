# Stage 1: Install Poetry
FROM python:3.12-slim AS poetry-installer

# Install curl (and clean apt caches)
RUN apt-get update \
 && apt-get install -y --no-install-recommends curl \
 && rm -rf /var/lib/apt/lists/*

ENV POETRY_HOME=/opt/poetry
ENV PATH="/opt/poetry/bin:$PATH"

# Pin or take latest; here we take the official installer’s latest
RUN curl -sSL https://install.python-poetry.org | python3 -

# Stage 2: Build the application
FROM python:3.12-slim AS app

# (Optional) install system build deps only if you compile wheels
# RUN apt-get update && apt-get install -y --no-install-recommends build-essential && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Bring in Poetry
COPY --from=poetry-installer /opt/poetry /opt/poetry
ENV PATH="/opt/poetry/bin:$PATH"

# Configure Poetry to install into the system site-packages (no venv = smaller image)
ENV POETRY_VIRTUALENVS_CREATE=false \
    POETRY_NO_INTERACTION=1

# Copy dependency files first for better layer caching
# Ensure poetry.lock exists in repo; if not, run `poetry lock` locally and commit it.
COPY pyproject.toml poetry.lock ./

# Install app deps (no dev)
RUN poetry install --no-dev --no-ansi

# Copy the rest of the app
COPY . .

# Default command
CMD ["python", "main.py"]

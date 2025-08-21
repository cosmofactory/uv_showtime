# Stage 1: Install Poetry
FROM python:3.11-slim as poetry-installer

RUN apt-get update && apt-get install -y curl

ENV POETRY_HOME=/opt/poetry
ENV PATH="/opt/poetry/bin:$PATH"
RUN curl -sSL https://install.python-poetry.org | python3 -

# Stage 2: Build the application
FROM python:3.11-slim

# Set the working directory
WORKDIR /app

# Copy poetry and pyproject.toml
COPY --from=poetry-installer /opt/poetry /opt/poetry
ENV PATH="/opt/poetry/bin:$PATH"
COPY pyproject.toml poetry.lock* ./

# Install dependencies
RUN poetry install --no-dev --no-interaction --no-ansi

# Copy the rest of the application
COPY main.py .

# Command to run the application
CMD ["poetry", "run", "python", "main.py"]

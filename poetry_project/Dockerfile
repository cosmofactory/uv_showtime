# Stage 1: Install Poetry
FROM python:3.11-slim as poetry-installer

# Install poetry
RUN pip install poetry

# Stage 2: Build the application
FROM python:3.11-slim

# Set the working directory
WORKDIR /app

# Copy poetry and pyproject.toml
COPY --from=poetry-installer /root/.local/bin/poetry /usr/local/bin/poetry
COPY pyproject.toml poetry.lock* ./

# Install dependencies
RUN poetry install --no-dev --no-interaction --no-ansi

# Copy the rest of the application
COPY main.py .

# Command to run the application
CMD ["poetry", "run", "python", "main.py"]

FROM python:3.12-slim AS poetry-installer

RUN apt-get update \
 && apt-get install -y --no-install-recommends curl \
 && rm -rf /var/lib/apt/lists/*

ENV POETRY_HOME=/opt/poetry
ENV PATH="/opt/poetry/bin:$PATH"

RUN curl -sSL https://install.python-poetry.org | python3 -

FROM python:3.12-slim AS app


WORKDIR /app

COPY --from=poetry-installer /opt/poetry /opt/poetry
ENV PATH="/opt/poetry/bin:$PATH"

ENV POETRY_VIRTUALENVS_CREATE=false \
    POETRY_NO_INTERACTION=1

COPY pyproject.toml poetry.lock ./


RUN poetry install


COPY . .


CMD ["python", "main.py"]

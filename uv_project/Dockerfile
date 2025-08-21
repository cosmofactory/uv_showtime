FROM python:3.13-slim-bookworm

COPY --from=ghcr.io/astral-sh/uv:0.6.1 /uv /uvx /bin/

WORKDIR /app
ENV UV_COMPILE_BYTECODE=1

ENV UV_LINK_MODE=copy

RUN --mount=type=cache,target=/root/.cache/uv \
    --mount=type=bind,source=uv.lock,target=uv.lock \
    --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv sync --frozen --no-install-project --no-dev

ADD . /app
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen --no-dev

# Copy the rest of the application
COPY main.py .

# Command to run the application
CMD ["uv", "run", "python", "main.py"]

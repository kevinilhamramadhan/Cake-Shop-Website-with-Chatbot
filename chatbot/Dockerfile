FROM ghcr.io/astral-sh/uv:python3.13-slim as base
WORKDIR /app
COPY pyproject.toml uv.lock ./
RUN uv sync --frozen --no-cache

FROM base as image
WORKDIR /app
RUN apt-get update && \
    apt-get install -y dumb-init && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
RUN useradd --create-home --shell /bin/bash python
COPY --from=builder /app/.venv /app/.venv
COPY ..
RUN chown -R python:python /app
USER python

ENV PATH="/app/.venv/bin:$PATH" \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

EXPOSE 8000

ENTRYPOINT ["dumb-init", "--"]
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "1"]

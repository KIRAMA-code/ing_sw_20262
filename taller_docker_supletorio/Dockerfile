
FROM python:3.14-slim AS builder


COPY --from=ghcr.io/astral-sh/uv:0.12.17 /uv /usr/local/bin/uv

WORKDIR /build

COPY pyproject.toml uv.lock ./


ENV UV_PROJECT_ENVIRONMENT=/opt/venv
RUN uv venv /opt/venv && \
    uv sync --frozen --no-dev


FROM python:3.14-slim

RUN useradd --create-home --shell /bin/bash reservas

WORKDIR /srv/reservas


COPY --from=builder /opt/venv /srv/reservas/.venv


COPY reservas/ ./reservas/


RUN chown -R reservas:reservas /srv/reservas


ENV PATH="/srv/reservas/.venv/bin:$PATH"

USER reservas

EXPOSE 8500

HEALTHCHECK --interval=10s --timeout=3s --start-period=20s --retries=3 \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8500/estado')" || exit 1

CMD ["uvicorn", "reservas.api:app", "--host", "0.0.0.0", "--port", "8500"]

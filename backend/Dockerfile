FROM python:3.11

RUN --mount=type=cache,target=/var/cache/apt \
    set -x && \
    apt-get -qq update && \
    apt-get -qq install -y git && \
    useradd -m app -d /app

USER app

RUN --mount=type=cache,target=/root/.cache/pip \
    curl -sSL https://install.python-poetry.org | python3 -


ARG REPO_COMMIT_SHA=main

RUN --mount=type=cache,target=/app/.cache/pip \
    --mount=type=cache,target=/tmp \
    set -x && \
    git clone https://github.com/OpenDevin/OpenDevin.git /app/src && \
    cd /app/src && \
    git checkout $REPO_COMMIT_SHA && \
    ~/.local/bin/poetry install --without evaluation

COPY config.toml /app/src/config.toml

WORKDIR /app/src
EXPOSE 3000
CMD [ \
    "/app/.local/bin/poetry", \
    "run", \
    "uvicorn", \
    "opendevin.server.listen:app", \
    "--port", "3000", \
    "--host", "0.0.0.0" \
]
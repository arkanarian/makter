FROM python:3.9-slim

ARG UID=1000 \
  GID=1000

ENV PYTHONUNBUFFERED=1 \
  PYTHONDONTWRITEBYTECODE=1 \
  PYTHONPATH='/backend' \
  # poetry:
  POETRY_VERSION=1.2.0 \
  POETRY_NO_INTERACTION=1 \
  POETRY_VIRTUALENVS_CREATE=false \
  POETRY_HOME='/usr/local'

EXPOSE 8000

WORKDIR /backend

RUN apt-get update && apt-get upgrade -y \
  && apt-get install --no-install-recommends -y \
    curl \
  # Installing 'poetry' package manager:
  && curl -sSL 'https://install.python-poetry.org' | python - \
  && poetry --version \
  # Add user, group \
  && groupadd -g "${GID}" -r web \
  && useradd -d '/backend' -g web -l -r -u "${UID}" web \
  && chown web:web -R '/backend'
  # Create static and media folders
  # && mkdir -p '/var/www/django/static' '/var/www/django/media' \
  # && chown web:web '/var/www/django/static' '/var/www/django/media'

# Copy poetry libraries to cache them in docker layer
COPY --chown=web:web poetry.lock pyproject.toml /backend/

# Poetry libraries installation
RUN poetry run pip install -U pip \
  && poetry config virtualenvs.create $POETRY_VIRTUALENVS_CREATE \
  && poetry install --no-interaction --no-ansi --no-root

# Copy project to the docker layer
COPY --chown=web:web . /backend/
COPY --chown=web:web ./scripts/core-entrypoint.sh /core-entrypoint.sh

RUN ["chmod", "+x", "/core-entrypoint.sh"]
#RUN chown web:web -R '/backend/'

# Running as non-root user
USER web

ENTRYPOINT [ "/core-entrypoint.sh" ]

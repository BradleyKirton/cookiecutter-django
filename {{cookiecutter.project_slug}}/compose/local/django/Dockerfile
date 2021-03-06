{%- if cookiecutter.python_version == "3.6" %}
FROM python:3.6.8
{% elif cookiecutter.python_version == "3.7" %}
FROM python:3.7.3
{% endif -%}

ENV PYTHONUNBUFFERED 1
ENV PYTHONDONTWRITEBYTECODE 1

RUN apt-get update
RUN apt-get -y install \
	libsass-dev \
{%- if cookiecutter.pdf_plugin == "y" %}
	libcairo2 libpango-1.0-0 \
	libpangocairo-1.0-0 libgdk-pixbuf2.0-0 \
	libffi-dev shared-mime-info
{%- endif %}

# Requirements are installed here to ensure they will be cached.
RUN pip install --upgrade pip poetry
COPY ./pyproject.toml /pyproject.toml
RUN poetry lock
RUN poetry install

COPY ./compose/production/django/entrypoint /entrypoint
RUN sed -i 's/\r//' /entrypoint
RUN chmod +x /entrypoint

COPY ./compose/local/django/start /start
RUN sed -i 's/\r//' /start
RUN chmod +x /start
{% if cookiecutter.use_celery == "y" %}
COPY ./compose/local/django/celery/worker/start /start-celeryworker
RUN sed -i 's/\r//' /start-celeryworker
RUN chmod +x /start-celeryworker

COPY ./compose/local/django/celery/beat/start /start-celerybeat
RUN sed -i 's/\r//' /start-celerybeat
RUN chmod +x /start-celerybeat

COPY ./compose/local/django/celery/flower/start /start-flower
RUN sed -i 's/\r//' /start-flower
RUN chmod +x /start-flower
{% endif %}
WORKDIR /app

ENTRYPOINT ["/entrypoint"]

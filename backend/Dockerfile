FROM python:3.8.1-slim

ENV PYTHONUNBUFFERED 1

EXPOSE 8000
WORKDIR /usr/app


RUN apt-get update && \
    apt-get install -y --no-install-recommends netcat && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY poetry.lock pyproject.toml ./

RUN pip install poetry==1.1
RUN poetry config virtualenvs.in-project false
RUN poetry install --no-dev

COPY . ./

ENTRYPOINT ["bash", "entrypoint.sh"]

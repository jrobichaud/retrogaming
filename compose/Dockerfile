ARG PYTHON_VERSION

FROM python:${PYTHON_VERSION}-alpine as python

FROM python AS python-build-stage

RUN apk update \
  && apk add --virtual build-dependencies \
     build-base

COPY ./src/requirements.txt /requirements.txt

RUN pip wheel --wheel-dir /usr/src/app/wheels \
  -r /requirements.txt

FROM python AS python-run-stage

ENV PYTHONUNBUFFERED 1

WORKDIR /app

COPY --from=python-build-stage /usr/src/app/wheels  /wheels/

RUN pip install --no-cache-dir --no-index --find-links=/wheels/ /wheels/* \
	&& rm -rf /wheels/

COPY ./compose/start /start
RUN sed -i 's/\r//' /start
RUN chmod +x /start

RUN addgroup --system django \
    && adduser --system --ingroup django django
RUN mkdir /src
RUN chown django:django /app
RUN chown django:django /src

USER django

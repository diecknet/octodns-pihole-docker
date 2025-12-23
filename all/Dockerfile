FROM python:3.14.1-slim AS build

WORKDIR /opt/octodns

RUN apt-get update && apt-get install -y git

COPY requirements.txt all-requirements.txt

ARG OCTODNS_FLAVOR=octodns

RUN set -ex; \
  if [ "${OCTODNS_FLAVOR}" != "octodns" ] ; then \
    head -n1 all-requirements.txt >> requirements.txt \
    && grep "${OCTODNS_FLAVOR}" all-requirements.txt >> requirements.txt; \
  else \
    cp all-requirements.txt requirements.txt; \
  fi

RUN set -ex \
  && python -m venv env \
  && . env/bin/activate \
  && pip install --no-cache-dir -r requirements.txt

# Stage 2: Final image
FROM python:3.14.1-slim

WORKDIR /opt/octodns

# Copy installed environment from build stage
COPY --from=build /opt/octodns /opt/octodns

ENV PATH=/opt/octodns/env/bin:/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
CMD [ "/bin/sh" ]

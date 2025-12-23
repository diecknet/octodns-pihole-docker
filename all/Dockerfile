FROM python:3.14.1-slim AS build

WORKDIR /opt/octodns

RUN apt-get update && apt-get install -y git ca-certificates && update-ca-certificates

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
  && pip install --upgrade pip \
  && git config --global credential.helper "" \
  && git config --global url."https://github.com/".insteadOf "git@github.com:" \
  # NOTE: SSL verification bypass is required in GitHub Actions CI environment
  # due to self-signed certificates in the certificate chain. In production
  # environments, proper CA certificates should be used instead.
  && GIT_SSL_NO_VERIFY=1 GIT_TERMINAL_PROMPT=0 pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org --no-cache-dir -r requirements.txt

# Stage 2: Final image
FROM python:3.14.1-slim

WORKDIR /app

# Copy installed environment from build stage
COPY --from=build /opt/octodns /opt/octodns

ENV PATH=/opt/octodns/env/bin:/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
CMD [ "/bin/sh" ]

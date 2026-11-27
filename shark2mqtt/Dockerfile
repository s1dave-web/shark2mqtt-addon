# Shark2MQTT (s1dave) Home Assistant add-on.
#
# Builds FROM the upstream shark2mqtt image (for its Chromium, Xvfb and
# Python packages), then swaps in the source of the s1dave-web/shark2mqtt
# fork at SHARK2MQTT_FORK_REF and replaces the entrypoint with a Home
# Assistant-aware run script.
#
# Home Assistant writes /data/options.json in a way that may only be
# readable by root. This wrapper intentionally starts as root so it can
# read add-on options, create /tmp/.X11-unix, and persist tokens in /data.
ARG SHARK2MQTT_VERSION=1.7.0
FROM ghcr.io/camsoper/shark2mqtt:${SHARK2MQTT_VERSION}

# Commit (or tag) of github.com/s1dave-web/shark2mqtt to run
ARG SHARK2MQTT_FORK_REF=87ad344c513cbfaa7cd32325b157d12745e07ddc

USER root

RUN apt-get update \
    && apt-get install -y --no-install-recommends jq curl ca-certificates \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir /tmp/fork \
    && curl -fsSL "https://github.com/s1dave-web/shark2mqtt/archive/${SHARK2MQTT_FORK_REF}.tar.gz" \
       | tar -xz --strip-components=1 -C /tmp/fork \
    && pip install --no-cache-dir -r /tmp/fork/requirements.txt \
    && rm -rf /app/src && cp -r /tmp/fork/src /app/src \
    && rm -rf /tmp/fork

COPY run.sh /run.sh
RUN chmod 755 /run.sh

ENV PYTHONUNBUFFERED=1

ENTRYPOINT ["/run.sh"]

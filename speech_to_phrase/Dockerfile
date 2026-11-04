# Base image updated by Renovate, update versionCompatibility on Debian base bump
ARG BUILD_FROM=ghcr.io/home-assistant/base-debian:trixie-2026.10.3@sha256:a9fb29d0ffab1a81d6379e6252db21e0edbd1263c94d9de2cfc54a396aad8611
FROM ${BUILD_FROM}
ARG TARGETARCH

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install speech-to-phrase
ARG SPEECH_TO_PHRASE_VERSION=1.4.3
ARG ONNXRUNTIME_VERSION=1.29.0
ARG PYSILERO_VAD_VERSION=2.1.1
WORKDIR /usr/src

RUN mkdir -p ./tools

RUN \
    apt-get update \
    && apt-get install -y --no-install-recommends \
        netcat-traditional \
        python3 \
        python3-pip \
        python3-dev \
        python3-venv \
        build-essential \
        curl \
        libopenblas0 \
        libencode-perl \
    && curl --location --output - \
        "https://huggingface.co/datasets/rhasspy/rhasspy-speech/resolve/main/tools/rhasspy-speech_${TARGETARCH}.tar.gz?download=true" | \
        tar -C ./tools -xzf - \
    && python3 -m venv .venv \
    && .venv/bin/pip3 install --no-cache-dir \
        "speech-to-phrase @ https://github.com/OHF-Voice/speech-to-phrase/archive/refs/tags/v${SPEECH_TO_PHRASE_VERSION}.tar.gz" \
        "onnxruntime==${ONNXRUNTIME_VERSION}" \
        "pysilero-vad==${PYSILERO_VAD_VERSION}" \
    && apt-get remove --yes build-essential \
    && apt-get autoclean \
    && apt-get purge \
    && rm -rf /var/lib/apt/lists/*

ENV ORT_DISABLE_TELEMETRY=1

WORKDIR /
COPY rootfs /

HEALTHCHECK --start-period=10m --start-interval=5s --interval=30s \
    CMD echo '{ "type": "describe" }' \
        | nc -w 1 localhost 10300 \
        | grep -iq "speech-to-phrase" \
        || exit 1

LABEL \
    org.opencontainers.image.title="Home Assistant App: Speech-to-Phrase" \
    org.opencontainers.image.description="Fast and personalized local speech-to-text" \
    org.opencontainers.image.source="https://github.com/home-assistant/app-speech-to-phrase" \
    org.opencontainers.image.licenses="Apache License 2.0"

ARG TAG=20.04

FROM ubuntu:${TAG}

ARG TAG

ARG BAZELISK_VERSION=1.25.0
ARG GH_VERSION=2.66.1
ARG ARCH=amd64
ARG BUILDBOT_NAME=buildbot

RUN : \
  && apt-get update \
  && DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y \
  curl \
  sudo \
  gcc \
  g++ \
  locales \
  ca-certificates \
  git \
  python3 \
  zlib1g-dev \
  patchelf \
  libedit-dev \
  && locale-gen en_US.UTF-8 \
  && update-ca-certificates \
  && rm -rf /var/lib/apt/lists/*

RUN if [ "$TAG" = "20.04" ]; then apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y clang-18 lld-18 && rm -rf /var/lib/apt/lists/*; fi

RUN : \
  mkdir /workspace \
  && useradd -c "" -u 1001 -m ${BUILDBOT_NAME} \
  && echo ${BUILDBOT_NAME} ALL=\(ALL\) NOPASSWD:ALL > /etc/sudoers.d/${BUILDBOT_NAME}

RUN : \
  && curl -Ls https://github.com/bazelbuild/bazelisk/releases/download/v${BAZELISK_VERSION}/bazelisk-linux-${ARCH} -o /usr/local/bin/bazelisk \
  && curl -Ls https://github.com/cli/cli/releases/download/v${GH_VERSION}/gh_${GH_VERSION}_linux_${ARCH}.tar.gz | tar xzf - --strip=1 -C /usr/local \
  && chmod 755 /usr/local/bin/bazelisk /usr/local/bin/gh

USER ${BUILDBOT_NAME}
WORKDIR /workspace

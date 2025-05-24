ARG TAG=20.04

FROM ubuntu:${TAG}

ARG TAG

ARG BAZELISK_VERSION=1.25.0
ARG GH_VERSION=2.66.1
ARG TARGETARCH
ARG BUILDBOT_NAME=buildbot
ARG CROSSTOOL_VERSION=1.27.0
ARG CMAKE_VERSION=4.0.0
ARG RUST_VERSION=1.86.0
ARG GO_VERSION=1.24.2

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
  python3-dev \
  gperf \
  zlib1g-dev \
  patchelf \
  libedit-dev \
  rsync \
  bison \
  flex \
  texinfo \
  help2man \
  make \
  libncurses5-dev \
  autoconf \
  automake \
  libtool \
  libtool-bin \
  gawk \
  wget \
  bzip2 \
  xz-utils \
  unzip \
  patch \
  libstdc++6 \
  rsync \
  meson \
  ninja-build \
  lzip \
  && locale-gen en_US.UTF-8 \
  && update-ca-certificates \
  && rm -rf /var/lib/apt/lists/*

RUN if [ "$TAG" = "20.04" ]; then apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y clang-18 lld-18 && rm -rf /var/lib/apt/lists/*; fi

RUN : \
  && mkdir /workspace \
  && useradd -c "" -u 1001 -m ${BUILDBOT_NAME} \
  && echo ${BUILDBOT_NAME} ALL=\(ALL\) NOPASSWD:ALL > /etc/sudoers.d/${BUILDBOT_NAME}

RUN : \
  && curl -Ls https://go.dev/dl/go${GO_VERSION}.linux-${TARGETARCH}.tar.gz | tar xfz - -C /usr/local \
  && curl -Ls https://github.com/bazelbuild/bazelisk/releases/download/v${BAZELISK_VERSION}/bazelisk-linux-${TARGETARCH} -o /usr/local/bin/bazelisk \
  && curl -Ls https://github.com/cli/cli/releases/download/v${GH_VERSION}/gh_${GH_VERSION}_linux_${TARGETARCH}.tar.gz | tar xzf - --strip=1 -C /usr/local \
  && chmod 755 /usr/local/bin/bazelisk /usr/local/bin/gh

RUN if [ "$TARGETARCH" = "amd64" ]; then curl -Ls https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.tar.gz | tar xfz - --strip=1 -C /usr/local; fi
RUN if [ "$TARGETARCH" = "arm64" ]; then curl -Ls https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-aarch64.tar.gz | tar xfz - --strip=1 -C /usr/local; fi

RUN : \
  && curl -Ls http://crosstool-ng.org/download/crosstool-ng/crosstool-ng-${CROSSTOOL_VERSION}.tar.xz | tar xfJ - -C /tmp/ \
  && cd /tmp/crosstool-ng-${CROSSTOOL_VERSION} \
  && ./configure --prefix=/usr/local \
  && make \
  && make install \
  && rm -rf /tmp/crosstool-ng-${CROSSTOOL_VERSION}

USER ${BUILDBOT_NAME}

RUN : \
  && curl https://sh.rustup.rs -sSf | sh -s -- -y --no-modify-path --profile minimal --default-toolchain ${RUST_VERSION} --target aarch64-apple-darwin

ENV PATH=/usr/local/go/bin:$PATH

WORKDIR /workspace

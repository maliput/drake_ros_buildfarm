FROM ubuntu:jammy
MAINTAINER Jose Luis Rivero <jrivero@openrobotics.org>
ENV LANG C
ENV LC_ALL C
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
 bazel-bootstrap \
 wget \
 unzip \
 zip \
 zlib1g-dev \
 && rm -rf /var/lib/apt/lists/*

ARG user=buildfarm
ARG group=buildfarm
ARG uid=1000
ARG gid=1000
RUN groupadd -g ${gid} ${group}
RUN useradd -u ${uid} -g ${group} -s /bin/sh -m ${user}
USER ${uid}:${gid}
WORKDIR /home/buildfarm

RUN wget https://github.com/bazelbuild/bazel/archive/refs/tags/4.2.3.tar.gz \
    && tar xzf 4.2.3.tar.gz \
    && echo "a9ef01b744d2fdcd688dcc9ecc25ad6dffaf4a9967058303fd27a5b41670b7d7 4.2.3.tar.gz" | sha256sum -c
WORKDIR bazel-4.2.3
COPY patches/absl_ghrp.patch absl_ghrp.patch
COPY pacthes/ijar.patch ijar.patch
RUN patch -p1 < absl_ghrp.patch \
    && patch -p1 < ijar.patch
RUN bazel build //src:bazel-dev

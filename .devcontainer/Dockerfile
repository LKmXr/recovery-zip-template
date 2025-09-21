# syntax=docker/dockerfile:1
# escape=\

FROM debian:trixie-slim AS recovery_zip_template_container

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
  apt-get full-upgrade -y

RUN apt-get install --no-install-recommends -y \
  apt-transport-https busybox ca-certificates curl \
  gpg unzip openssl shellcheck sudo wget xz-utils zip ziptime

ARG TMPDIR=/tmp

WORKDIR $TMPDIR

RUN wget -qO- https://7-zip.org/a/7z2501-linux-x64.tar.xz | tar -xJvf -

RUN (./7zz && ./7zzs) >/dev/null

RUN cp -af ./7zz ./7zzs /usr/local/bin

RUN ln -sf 7zz /usr/local/bin/7z && \
  ln -sf 7zzs /usr/local/bin/7zs

RUN wget -qO- https://packages.adoptium.net/artifactory/api/gpg/key/public | \
  gpg --dearmor | tee /etc/apt/trusted.gpg.d/adoptium.gpg >/dev/null

RUN echo "deb https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | \
  tee /etc/apt/sources.list.d/adoptium.list

RUN apt-get update && \
  apt-get install --no-install-recommends -y temurin-24-jdk

ENV ANDROID_HOME=/root/Library/Android/sdk

RUN wget -qO cmdline-tools.zip https://dl.google.com/android/repository/commandlinetools-linux-13114758_latest.zip && \
  unzip -o ./cmdline-tools.zip

WORKDIR $ANDROID_HOME/cmdline-tools/latest

RUN cp -af $TMPDIR/cmdline-tools/. .

ENV PATH="$ANDROID_HOME/cmdline-tools/latest/bin:$PATH"

RUN yes | sdkmanager --licenses >/dev/null

RUN sdkmanager "build-tools;35.0.0"

ENV PATH="$ANDROID_HOME/build-tools/35.0.0:$PATH"

WORKDIR $TMPDIR

RUN rm -rf 7zz* *.txt MANUAL cmdline-tools*

ARG WORKSPACE_DIR=/project/workspace

WORKDIR $WORKSPACE_DIR

ENV PATH="$WORKSPACE_DIR/bin:$WORKSPACE_DIR/.bin:$WORKSPACE_DIR/tools:$PATH"

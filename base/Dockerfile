FROM docker.io/library/debian:bookworm-slim

ENV S6_BEHAVIOUR_IF_STAGE2_FAILS=2

COPY rootfs /

RUN \
  dpkgArch="$(dpkg --print-architecture)" && \
  case "${dpkgArch##*-}" in \
  "amd64") TPL_ARCH=amd64; S6_ARCH=amd64 ;; \
  "arm64") TPL_ARCH=arm64; S6_ARCH=aarch64 ;; \
  *) echo "unsupported architecture"; exit 1 ;; \
  esac && \
  apt-dpkg-wrap apt-get update && \
  apt-dpkg-wrap apt-get install -y apt-transport-https apt-utils ca-certificates gnupg wget curl && \
  wget -qO /usr/bin/tpl https://github.com/jitsi/tpl/releases/download/v1.4.0/tpl-linux-${TPL_ARCH} && \
  # Workaround S6 bug when /bin is a symlink
  wget -qO /tmp/s6.tar.gz https://github.com/just-containers/s6-overlay/releases/download/v1.22.1.0/s6-overlay-${S6_ARCH}.tar.gz && \
  mkdir /tmp/s6 && \
  tar xfz /tmp/s6.tar.gz -C /tmp/s6 && \
  tar hxfz /tmp/s6.tar.gz -C / && \
  rm -f /usr/bin/execlineb && \
  cp /tmp/s6/bin/execlineb /usr/bin/ && \
  rm -rf /tmp/s6* && \
  apt-dpkg-wrap apt-get update && \
  apt-dpkg-wrap apt-get dist-upgrade -y && \
  apt-cleanup && \
  chmod +x /usr/bin/tpl

ENTRYPOINT [ "/init" ]
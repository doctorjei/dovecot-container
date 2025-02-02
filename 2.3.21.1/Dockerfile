FROM debian:12-slim

LABEL org.opencontainers.image.authors="dovecot@dovecot.org, jjb@eng.ufl.edu"

ENV container=docker \
    PUID=1000 \
    PGID=1000 \
    LC_ALL=C
    
ARG DEBIAN_FRONTEND=noninteractive

RUN apt -y update && apt -y install \
  tini \
  dovecot-core \
  dovecot-fts-xapian \
  dovecot-gssapi \
  dovecot-imapd \
  dovecot-ldap \
  dovecot-lmtpd \
  dovecot-managesieved \
  dovecot-mysql \
  dovecot-pgsql \
  dovecot-sieve \
  dovecot-sqlite \
  dovecot-submissiond \
  mupdf-tools \
  ca-certificates \
  ssl-cert && \
  rm -rf /var/lib/apt/lists && \
  groupadd -g 1000 vmail && \
  useradd -u ${PUID} -g ${PGID} vmail -d /srv/vmail && \
  passwd -l vmail && \
  rm -rf /etc/dovecot && \
  mkdir /srv/mail && \
  chown vmail:vmail /srv/mail && \
  make-ssl-cert generate-default-snakeoil && \
  mkdir /etc/dovecot && \
  ln -s /etc/ssl/certs/ssl-cert-snakeoil.pem /etc/dovecot/cert.pem && \
  ln -s /etc/ssl/private/ssl-cert-snakeoil.key /etc/dovecot/key.pem

COPY --chmod=755 decode2text.sh /usr/libexec/dovecot
ADD dovecot.conf /etc/dovecot/dovecot.conf

EXPOSE 24
EXPOSE 110
EXPOSE 143
EXPOSE 587
EXPOSE 990
EXPOSE 993
EXPOSE 4190

VOLUME ["/etc/dovecot", "/srv/mail"]
ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["/usr/sbin/dovecot", "-F"]

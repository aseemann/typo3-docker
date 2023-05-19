FROM aseemann/t3re:81

RUN apk add --no-cache git wget openssh-client openssl zip unzip shadow sudo bash

COPY rootfs/ /
RUN chmod +x /scripts -R

RUN /scripts/install-composer.sh
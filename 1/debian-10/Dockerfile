FROM docker.io/bitnami/minideb:buster
LABEL maintainer "Bitnami <containers@bitnami.com>"

ENV HOME="/" \
    OS_ARCH="amd64" \
    OS_FLAVOUR="debian-10" \
    OS_NAME="linux"

COPY prebuildfs /
# Install required system packages and dependencies
RUN install_packages acl ca-certificates curl gzip libbz2-1.0 libc6 libffi6 libgcc1 libgomp1 liblzma5 libncursesw6 libreadline7 libsqlite3-0 libssl1.1 libstdc++6 libtinfo6 procps tar zlib1g
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "python" "3.7.12-31" --checksum 09d527409470507c89fca4f764a2ba54fdc9f27879c61e5c77d5df943d4e51ba
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "pytorch" "1.10.2-2" --checksum 0cabab9a07d8c3b029dc118b82382de42488cb0e04436d230eaf44cebf9ccb26
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "gosu" "1.14.0-4" --checksum ec62006ece7e0b49c65926e458ba2b71d6d251552118b1378840b1803dbb3d53
RUN apt-get update && apt-get upgrade -y && \
    rm -r /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami

COPY rootfs /
RUN /opt/bitnami/scripts/pytorch/postunpack.sh
ENV BITNAMI_APP_NAME="pytorch" \
    BITNAMI_IMAGE_VERSION="1.10.2-debian-10-r14" \
    LD_LIBRARY_PATH="/opt/bitnami/python/lib/python3.7/site-packages/torch/lib/:/opt/bitnami/python/lib/python3.7/site-packages/PIL/.libs/:$LD_LIBRARY_PATH" \
    PATH="/opt/bitnami/python/bin:/opt/bitnami/common/bin:$PATH"

WORKDIR /app

RUN install_packages ffmpeg python3.7-dev
RUN pip install --upgrade pip && pip install backgroundremover
RUN pip uninstall -y certifi && pip install certifi

# Download a test image and the ML models to verify the removal process works
RUN curl "https://frenmoji-media.s3.amazonaws.com/backgroundremover/fren.png" -o fren.png && \
    mkdir ~/.u2net/ && \
    curl "https://frenmoji-media.s3.amazonaws.com/backgroundremover/u2netp.pth" -o ~/.u2net/u2netp.pth && \
    curl "https://frenmoji-media.s3.amazonaws.com/backgroundremover/u2net.pth" -o ~/.u2net/u2net.pth && \
    backgroundremover -i fren.png -m "u2netp" -o fren-sticker.png && \
    backgroundremover -i fren.png -m "u2net" -o fren-sticker.png && \
    rm fren.png fren-sticker.png

CMD [ "backgroundremover" ]

FROM imriss/deepdetect_archlinux:compact

RUN echo 'building CPU Arch Linux DeepDetect image'

MAINTAINER Reza Farrahi <imriss@ieee.org>
LABEL description="DeepDetect deep learning server & API / CPU version / Arch Linux"

USER dd
WORKDIR /opt/deepdetect/build/main
CMD ./dede -host 0.0.0.0
EXPOSE 8080

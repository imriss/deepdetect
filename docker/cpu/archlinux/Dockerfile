FROM imriss/archlinux

RUN echo 'building CPU Arch Linux DeepDetect image'

MAINTAINER Reza Farrahi <imriss@ieee.org>
LABEL description="DeepDetect deep learning server & API / CPU version / Arch Linux"

RUN ln -sf /dev/stdout /var/log/deepdetect.log
RUN ln -sf /dev/stderr /var/log/deepdetect.log

RUN  pacman -Syyu --noconfirm \
	&& pacman -S findutils nano vi --needed --noconfirm \
	&& pacman-db-upgrade \
  && export editor=nano \
  && pacman -S --needed --noconfirm freetype2 systemd libglvnd 
  
RUN pacman -S --needed --noconfirm base-devel bash git cmake google-glog gflags eigen \
  opencv boost boost-libs openssl libcurl-compat curl protobuf  \
  hdf5 protobuf-c leveldb lmdb wget unzip

RUN ls -la /usr/sbin
# DDADD https://raw.githubusercontent.com/imriss/ZoneMinder/master/aur.sh /usr/sbin/aur.sh
# DDADD https://raw.githubusercontent.com/imriss/ZoneMinder/master/add-aur.sh /usr/sbin/add-aur
ADD ./aur.sh /usr/sbin/aur.sh
ADD ./add-aur.sh /usr/sbin/add-aur
RUN chmod u+x /usr/sbin/aur.sh
RUN chmod u+x /usr/sbin/add-aur
RUN add-aur docker

RUN su docker -c 'pacaur -S --needed --noprogressbar --noedit --noconfirm openblas-lapack utf8cpp asio libcurlpp'

# aur-e15211bf5b909285784dd5b2de48e53bbb4ae9d1 is cpp-netlib 0.11.2
RUN cd /home/docker \
  && mkdir cpp-netlib \
  && cd cpp-netlib \
  && wget https://aur.archlinux.org/cgit/aur.git/snapshot/aur-e15211bf5b909285784dd5b2de48e53bbb4ae9d1.tar.gz -O cpp-netlib.tar.gz \
  && tar -xvf cpp-netlib.tar.gz \
  && chmod 777 aur-e15211bf5b909285784dd5b2de48e53bbb4ae9d1 \
  && cd aur-e15211bf5b909285784dd5b2de48e53bbb4ae9d1 \
  && su docker -c 'makepkg -si --skippgpcheck --noconfirm '

WORKDIR /opt
RUN git clone --branch=patch-1 https://github.com/imriss/snappy.git
WORKDIR /opt/snappy
RUN ls -la
RUN ./autogen.sh \
  && ./configure \
  && make all
RUN make install

# RUN su docker -c 'pacaur -S --needed --noprogressbar --noedit --noconfirm snappy-git'

RUN useradd -ms /bin/bash dd

# RUN cp /usr/include/boost/asio/*.hpp /usr/include/\
#  && echo `find / -name 'buffer.hpp' 2>/dev/null`

WORKDIR /opt
RUN git clone https://github.com/beniz/deepdetect.git && cd deepdetect && mkdir build

WORKDIR /opt/deepdetect/build
RUN cmake .. -DUSE_XGBOOST=ON 
RUN make

# external volume to be mapped, e.g. for models or training data
RUN mkdir /data
VOLUME ["/data"]

# include a few image models within the image
RUN mkdir /opt/models
WORKDIR /opt/models
RUN mkdir ggnet && cd ggnet && wget http://www.deepdetect.com/models/ggnet/bvlc_googlenet.caffemodel
RUN mkdir resnet_50 && cd resnet_50 && wget http://www.deepdetect.com/models/resnet/ResNet-50-model.caffemodel && wget http://www.deepdetect.com/models/resnet/ResNet_mean.binaryproto
RUN cp /opt/deepdetect/datasets/imagenet/corresp_ilsvrc12.txt /opt/models/ggnet/corresp.txt
RUN cp /opt/deepdetect/datasets/imagenet/corresp_ilsvrc12.txt /opt/models/resnet_50/corresp.txt
RUN cp /opt/deepdetect/templates/caffe/googlenet/*prototxt /opt/models/ggnet/
RUN cp /opt/deepdetect/templates/caffe/resnet_50/*prototxt /opt/models/resnet_50/

# permissions
RUN chown -R dd:dd /opt/deepdetect
RUN chown -R dd:dd /opt/models



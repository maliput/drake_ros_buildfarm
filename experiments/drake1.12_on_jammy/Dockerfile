FROM ubuntu:jammy
MAINTAINER Jose Luis Rivero <jrivero@openrobotics.org>
ENV LANG C           
ENV LC_ALL C         
ARG DEBIAN_FRONTEND=noninteractive 
RUN apt-get update && apt-get install -y \
 bazel-bootstrap \
   clang-12 \
   clang-format-12 \
   cmake \
   coinor-libclp-dev \
   coinor-libcoinutils-dev \
   coinor-libipopt-dev \
   default-jdk \
   default-jre \
   dh-sequence-python3 \
   dh-sequence-sphinxdoc \
   doxygen \
   file \
   fonts-dejavu-core \
   fonts-dejavu-extra \
   gfortran \
   git \
   graphviz \
   javahelper \
   jekyll \
   jupyter-notebook \
   libamd2 \
   libblas-dev \
   libbz2-dev \
   libclang-12-dev \
   libcurl4-gnutls-dev \
   libdouble-conversion-dev \
   libeigen3-dev \
   libexpat1-dev \
   libfreetype6 \
   libgflags-dev \
   libgfortran5 \
   libgl-dev \
   libglew-dev \
   libglib2.0-dev \
   libglu1-mesa \
   libglx-dev \
   libhdf5-103 \
   libjchart2d-java \
   libjpeg-turbo8-dev \
   libjsoncpp-dev \
   liblapack-dev \
   libldl2 \
   liblz4-dev \
   liblzma-dev \
   libmsgpack-dev \
   libmumps-seq-dev \
   libnlopt-cxx-dev \
   libogg0 \
   libomp-12-dev \
   libopengl-dev \
   libpng-dev \
   libqt5core5a \
   libqt5gui5 \
   libqt5printsupport5 \
   libqt5widgets5 \
   libquadmath0 \
   libspdlog-dev \
   libsqlite3-0 \
   libsuitesparse-dev \
   libtheora0 \
   libtiff-dev \
   libtinyxml-dev \
   libtinyxml2-dev \
   libtool \
   libvtk9-dev \
   libyaml-cpp-dev \
   libx11-dev \
   libxml2 \
   libxt6 \
   node-uglify \
   ocl-icd-opencl-dev \
   opencl-headers \
   openssh-client \
   patch \
   patchelf \
   pkg-config \
   python-is-python3 \
   python3-all-dev \
   python3-docutils \
   python3-ipywidgets \
   python3-lxml \
   python3-matplotlib \
   python3-munkres \
   python3-numpy \
   python3-pil \
   python3-pydot \
   python3-pygame \
   python3-scipy \
   python3-semantic-version \
   python3-sphinx \
   python3-sphinx-rtd-theme \
   python3-tk \
   python3-tornado \
   python3-u-msgpack \
   python3-yaml \
   python3-zmq \
   rsync \
   unzip \
   wget \
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

RUN wget -q https://github.com/bazelbuild/bazel/archive/refs/tags/4.2.3.tar.gz \
    && tar xzf 4.2.3.tar.gz \
    && echo "a9ef01b744d2fdcd688dcc9ecc25ad6dffaf4a9967058303fd27a5b41670b7d7 4.2.3.tar.gz" | sha256sum -c
WORKDIR bazel-4.2.3
COPY patches/absl_ghrp.patch absl_ghrp.patch
COPY patches/ijar.patch ijar.patch
RUN patch -p1 < absl_ghrp.patch \
    && patch -p1 < ijar.patch
RUN bazel build //src:bazel --compilation_mode=opt

RUN ./bazel-bin/src/bazel version
RUN cp bazel-bin/src/bazel /tmp/bazel

RUN wget https://github.com/RobotLocomotion/drake/archive/refs/tags/v1.12.0.tar.gz \
  && tar xf v1.12.0.tar.gz
WORKDIR drake-1.12.0
RUN mkdir build \
    && cp /tmp/bazel build/
RUN mkdir gen/ \
    && echo "import tools/ubuntu-jammy.bazelrc" > gen/environment.bazelrc
RUN bazel clean
RUN mkdir -p build \
    && cd build \
    && '/usr/bin/cmake' '--warn-uninitialized' '-DCMAKE_BUILD_TYPE:STRING=Release' '-GUnix Makefiles' '-DCMAKE_FIND_USE_SYSTEM_ENVIRONMENT_PATH:BOOL=False' '..' \
    && make

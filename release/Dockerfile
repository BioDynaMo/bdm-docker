FROM ubuntu:20.04

# Set zone to avoid getting prompted during apt install
ENV TZ=Europe/Berlin
RUN if ! [ -L /etc/localtime ]; then \
      ln -fs /usr/share/zoneinfo/Europe/Berlin /etc/localtime; \
    fi

# Install prerequisites for BioDynaMo
RUN apt-get update && apt-get install -y --no-install-recommends \
  sudo \
  wget \
  curl \ 
  git \
  software-properties-common \
  gnupg \
  bsdmainutils

RUN curl -L -O https://github.com/Kitware/CMake/releases/download/v3.19.3/cmake-3.19.3-Linux-x86_64.sh
RUN chmod +x cmake-3.19.3-Linux-x86_64.sh
RUN sudo ./cmake-3.19.3-Linux-x86_64.sh --skip-license --prefix=/usr/local

# BioDynaMo is installed in $HOME by default
ENV HOME /

# Install BioDynaMo
RUN git clone https://github.com/BioDynaMo/biodynamo.git
RUN BDM_CMAKE_FLAGS="-Dnotebooks=on" SILENT_INSTALL=1 biodynamo/install.sh

# delete source and build files
RUN rm -rf /biodynamo

ENTRYPOINT ["/bin/bash", "-c", "source /biodynamo-v*/bin/thisbdm.sh && /bin/bash"]

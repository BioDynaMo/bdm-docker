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
  bsdmainutils \
  ninja-build

RUN curl -L -O https://github.com/Kitware/CMake/releases/download/v3.19.3/cmake-3.19.3-Linux-x86_64.sh
RUN chmod +x cmake-3.19.3-Linux-x86_64.sh
RUN sudo ./cmake-3.19.3-Linux-x86_64.sh --skip-license --prefix=/usr/local

RUN git clone https://github.com/BioDynaMo/biodynamo.git
RUN biodynamo/prerequisites.sh all

# Set pyenv python
RUN eval "$(pyenv init --path)"
RUN eval "$(pyenv init -)"
RUN pyenv global 3.9.1
RUN pyenv local 3.9.1

WORKDIR /biodynamo

RUN cmake -G Ninja \
    -Dparaview=ON \
    -DCMAKE_BUILD_TYPE=Release \
    -B build
RUN cmake --build build --parallel --config Release

ENTRYPOINT ["/bin/bash", "-c", "source /biodynamo/build/bin/thisbdm.sh && /bin/bash"]

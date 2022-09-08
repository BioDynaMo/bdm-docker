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
RUN SILENT_INSTALL=1 biodynamo/prerequisites.sh all

# Set pyenv python
ENV PYTHON_VERSION 3.9.1
ENV HOME /root
ENV PYENV_ROOT $HOME/.pyenv
ENV PATH "$PATH:${PYENV_ROOT}/shims:${PYENV_ROOT}/bin"

WORKDIR /biodynamo

# Activate pyenv in same command, because Docker runs each command as a separate shell, so it would see the pyenv python if run separately
RUN eval "$(pyenv init --path)" && eval "$(pyenv init -)" && pyenv global $PYTHON_VERSION && cmake -G Ninja \
  -Dparaview=ON \
  -DCMAKE_BUILD_TYPE=Release \
  -B build
RUN cmake --build build --parallel --config Release

ENTRYPOINT ["/bin/bash", "-c", "source /biodynamo/build/bin/thisbdm.sh && /bin/bash"]

FROM ubuntu:16.04

USER root:root

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV DEBIAN_FRONTEND noninteractive

# Base conda installation
ENV MINICONDA_VERSION 4.5.11
ENV PATH /opt/miniconda/bin:$PATH
RUN apt-get update -y && \
    apt-get install -y \
        bzip2 \
        wget \
        && \
    wget -qO /tmp/miniconda.sh https://repo.continuum.io/miniconda/Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh && \
    bash /tmp/miniconda.sh -bf -p /opt/miniconda && \
    conda clean -ay && \
    rm -rf /opt/miniconda/pkgs && \
    rm /tmp/miniconda.sh && \
    find / -type d -name __pycache__ | xargs rm -rf && \
    apt-get purge -y --auto-remove bzip2 wget && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*


# Intel MKL installation
ENV MKL_VERSION 2018.0.3
RUN conda install -y mkl=${MKL_VERSION} -c intel --no-update-deps && \
    conda clean -ay

# https://download.open-mpi.org/nightly/open-mpi/master/openmpi-master-201909240242-ee3564a.tar.gz
# Open-MPI installation
# ENV OPENMPI_VERSION 3.1.2
RUN apt-get update && apt-get install -y --no-install-recommends build-essential && apt-get clean && \
    apt-get install -y \
            bzip2 \
            wget \
            && \
    mkdir /tmp/openmpi && \
    cd /tmp/openmpi && \
    wget https://download.open-mpi.org/nightly/open-mpi/master/openmpi-master-201909240242-ee3564a.tar.gz && \
    tar zxf openmpi-master-201909240242-ee3564a.tar.gz && \
    cd openmpi-master-201909240242-ee3564a && \
    ./configure --enable-orterun-prefix-by-default && \
    make -j $(nproc) all && \
    make install && \
    ldconfig && \
    rm -rf /tmp/openmpi && \
    apt-get purge -y bzip2 wget && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

RUN apt-get update -y && \
    apt-get install -y \
        bzip2 \
        wget \
        && \
        wget https://github.com/Kitware/CMake/releases/download/v3.15.2/cmake-3.15.2-Linux-x86_64.sh && \
        bash cmake-3.15.2-Linux-x86_64.sh --skip-license --prefix=/usr && \
	rm cmake-3.15.2-Linux-x86_64.sh

RUN apt-get update -y && \
    apt-get install -y \
        git \
        && \
        git clone --recursive https://github.com/Microsoft/LightGBM && cd LightGBM && mkdir build && cd build && cmake -DUSE_MPI=ON .. && make -j4

ENV PATH="${PATH}:/LightGBM"

RUN [ "/bin/bash", "-c", "conda create -n lightgbm Python=3.6.2 && source activate lightgbm && pip install numpy==1.15.4 && pip install scipy==1.1.0 && pip install scikit-learn==0.20.1 && pip install azureml-defaults"]

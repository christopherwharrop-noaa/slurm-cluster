FROM ubuntu:20.04

RUN apt update -y \
&& apt install munge -y \
&& apt install vim -y \
&& apt install build-essential -y \
&& apt install git -y \
&& apt-get install mariadb-server -y \
&& apt install wget -y

RUN apt update -y \
&& apt install bc -y \
&& apt install ca-certificates -y \
&& apt-get --no-install-recommends -y install -o=Dpkg::Use-Pty=0 cmake \ 
&& apt-get --no-install-recommends -y install -o=Dpkg::Use-Pty=0 emacs \
&& apt install expat -y \
&& apt install file -y \
&& apt install gnupg -y \
&& apt install iproute2 -y \ 
&& apt-get --no-install-recommends -y install -o=Dpkg::Use-Pty=0 libarchive13 \
&& apt install libexpat1-dev -y \
&& apt install liblua5.3-dev -y \
&& apt install libpmi2-0-dev -y \
&& apt install libsqlite3-dev -y \
&& apt install libssl-dev -y \
&& apt install libtiff-dev -y \
&& apt install lmod -y \
&& apt install lua5.3 -y \
&& apt install lua-posix-dev -y \
&& apt install m4 -y \
&& apt install openssl -y \
&& apt install pkg-config -y \
&& apt install python3 -y \
&& apt install python3-pip -y \
&& apt install ruby-full -y \
&& apt install ruby-dev -y \
&& apt install slurm-client -y \
&& apt install ssh -y \
&& apt install tcl -y \
&& apt install tcl-dev -y \
&& apt install time -y \    
&& apt install xz-utils -y \
&& apt install zlib1g -y \
&& apt install zlib1g-dev -y \
&& update-alternatives --install /usr/bin/lua lua-interpreter /usr/bin/lua5.2 10 \
&& update-alternatives --install /usr/bin/python python $( which python3 ) 10 \
&& pip3 install Cython nose requests jinja2 pyyaml f90nml numpy netCDF4


ARG DEBIAN_FRONTEND=noninteractive
#RUN apt install slurm-client -y && apt install libpmi2-0-dev -y
#RUN apt install curl dirmngr apt-transport-https lsb-release ca-certificates -y
#RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
#RUN apt install sudo -y && apt install python3.9 python3-pip -y && useradd -m admin -s /usr/bin/bash -d /home/admin && echo "admin:admin" | chpasswd && adduser admin sudo && echo "admin     ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN apt install sudo -y && useradd -m admin -s /usr/bin/bash -d /home/admin && echo "admin:admin" | chpasswd && adduser admin sudo && echo "admin     ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

#RUN apt update -y && apt install nodejs -y && npm install -g configurable-http-proxy && pip3 install jupyterlab==2.1.2 

#COPY slurm.conf /etc/slurm-llnl/
#COPY cgroup.conf /etc/slurm-llnl/
#COPY docker-entrypoint.sh /etc/slurm-llnl/
#
#WORKDIR /home/admin
#
#EXPOSE 8888
#
#ENV USER admin
#ENV SHELL bash

#RUN apt install libopenmpi-dev -y && pip3 install mpi4py && pip3 install jupyterlab_slurm

# Add apt repository public key for Intel OneAPI
ARG url=https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB
ADD $url ./
RUN file=$(basename "$url") && \
    apt-key add "$file" && \
    rm "$file"

# Configure the apt repository
ARG repo=https://apt.repos.intel.com/oneapi
RUN echo "deb $repo all main" > /etc/apt/sources.list.d/oneAPI.list

# Install Intel oneapi packages and set up modules
RUN apt-get update -y && \
    apt-get install -y --no-install-recommends -o=Dpkg::Use-Pty=0 \
        intel-oneapi-dev-utilities \
        intel-oneapi-mpi-devel \
        intel-oneapi-openmp \
        intel-oneapi-compiler-fortran \
        intel-oneapi-compiler-dpcpp-cpp-and-cpp-classic && \
        echo "source /opt/intel/oneapi/setvars.sh" >> ~/.bash_profile
        
#RUN apt update -y               && \
#    apt install -y                 \
#      intel-oneapi-runtime-libs && \
#    echo "source /opt/intel/oneapi/setvars.sh" >> /home/admin/.bash_profile

SHELL ["/bin/bash", "--login", "-c"]

# Install Rocoto
RUN set -ex \
    && mkdir /opt/rocoto \
    && git clone -b develop https://github.com/christopherwharrop/rocoto.git /opt/rocoto/develop \
    && pushd /opt/rocoto/develop \
    && ./INSTALL

# Make a module for Rocoto
RUN set -ex \
    && mkdir /usr/share/lmod/lmod/modulefiles/rocoto \
    && echo '#%Module1.0' > /usr/share/lmod/lmod/modulefiles/rocoto/develop \
    && echo 'prepend-path PATH /opt/rocoto/develop/bin' >> /usr/share/lmod/lmod/modulefiles/rocoto/develop \
    && echo 'prepend-path MANPATH /opt/rocoto/develop/man' >> /usr/share/lmod/lmod/modulefiles/rocoto/develop


# Install hpc-stack
RUN set -ex \
    && git clone -b rrfs_ci https://github.com/NOAA-GSL/hpc-stack.git /tmp/hpc-stack \
    && pushd /tmp/hpc-stack \
    && prefix=/opt/hpc-stack \
    && python_ver=$( python3 --version | cut -d " " -f2 | cut -d. -f1-2 ) \
    && export HPC_COMPILER="intel/2021" \
    && export HPC_MPI="impi/2021" \
    && export HPC_PYTHON="python/${python_ver}" \
    && yes | ./setup_modules.sh -c config/config_custom.sh -p $prefix \
    && ./build_stack.sh -p $prefix -c config/config_custom.sh -y stack/stack_rrfs_ci.yaml -m \
    && popd \
    && rm -rf /tmp/hpc-stack


COPY slurm.conf /etc/slurm-llnl/
COPY cgroup.conf /etc/slurm-llnl/
COPY docker-entrypoint.sh /etc/slurm-llnl/

WORKDIR /home/admin

EXPOSE 8888

ENV USER admin
ENV SHELL bash

ENTRYPOINT ["/etc/slurm-llnl/docker-entrypoint.sh"]

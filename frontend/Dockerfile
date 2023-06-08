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
RUN apt install sudo -y && useradd -m admin -s /usr/bin/bash -d /home/admin && echo "admin:admin" | chpasswd && adduser admin sudo && echo "admin     ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

COPY slurm.conf /etc/slurm-llnl/
COPY cgroup.conf /etc/slurm-llnl/
COPY docker-entrypoint.sh /etc/slurm-llnl/

WORKDIR /home/admin

EXPOSE 8888

ENV USER admin
ENV SHELL bash

ENTRYPOINT ["/etc/slurm-llnl/docker-entrypoint.sh"]

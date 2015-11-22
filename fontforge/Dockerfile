#
# https://loicpefferkorn.net/2015/11/using-docker-to-build-and-run-a-bleeding-edge-version-of-a-software/
#

FROM debian:jessie
MAINTAINER Loic Pefferkorn <loic-docker@loicp.eu>
WORKDIR /root

# From official build instructions
# https://github.com/fontforge/fontforge/blob/master/INSTALL-git.md

# Fontforge build dependencies
RUN apt-get update && apt-get install -y \
    sudo \
    packaging-dev \
    pkg-config \
    python-dev \
    libpango1.0-dev \
    libglib2.0-dev \
    libxml2-dev \
    giflib-dbg \
    libjpeg-dev \
    libtiff-dev \
    uthash-dev \
    libspiro-dev \
    build-essential \
    automake \
    flex \
    bison && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*


# Optional: install fontforge runtime dependencies
RUN apt-get install -y $(apt-cache depends fontforge|awk '/Depends/ {print $2}')  && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
    
# Build libspiro dependency
RUN git clone https://github.com/fontforge/libspiro.git && \
     cd libspiro && \
     autoreconf -i && \
     automake --foreign -Wall && \
     ./configure --prefix=/usr  && \
     make install 

# Build libuninameslist dependency
RUN git clone https://github.com/fontforge/libuninameslist.git && \
     cd libuninameslist && \
     autoreconf -i && \
     automake --foreign && \
     ./configure --prefix=/usr && \
     make install

# Finally build Fontforge 
RUN git clone https://github.com/fontforge/fontforge.git && \
     cd fontforge && \
     ./bootstrap && \
     ./configure --prefix=/usr && \
     make install && \
     ldconfig

# Trick found on http://fabiorehm.com/blog/2014/09/11/running-gui-apps-with-docker/
# Figure out your own host id/gid and 
# update the values below accordingly
ENV uid 1000
ENV gid 100
ENV username developer
RUN /usr/sbin/useradd --uid $uid --gid $gid --create-home $username && \
    echo "$username ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/developer && \
    chmod 0440 /etc/sudoers.d/developer

USER developer
ENV HOME /home/developer
CMD /bin/bash
CMD /usr/bin/fontforge

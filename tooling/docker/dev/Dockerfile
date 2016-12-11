# A docker setup for DXR development
#
# This image has DXR and various compilers installed, so it can be used for
# indexing, serving web, and interactive debugging.
#
# This image should not be used as a base for production setups. Those would
# want separate images (and separate machines) for indexing. Also, we leave out
# some image size optimizations and introduce would-be security holes for the
# sake of a good dev experience.

FROM ubuntu:16.04

MAINTAINER Erik Rose <erik@mozilla.com>

COPY set_up_ubuntu.sh /tmp/
RUN DEBIAN_FRONTEND=noninteractive /tmp/set_up_ubuntu.sh

COPY set_up_common.sh /tmp/
RUN DEBIAN_FRONTEND=noninteractive /tmp/set_up_common.sh

# Give root a known password so devs can become root:
RUN echo "root:docker" | chpasswd

# Install Graphviz, needed only for building docs.
# Not running apt-get clean, to keep dev experience snappy.
RUN DEBIAN_FRONTEND=noninteractive apt-get -q -y install graphviz

RUN useradd --create-home --home-dir /home/dxr --shell /bin/bash --groups sudo --uid 1000 dxr
RUN echo "dxr:docker" | chpasswd
VOLUME /home/dxr/dxr

# Provide dxr user sudo permission: some source projects may need to install additional system packages.
RUN echo "%sudo ALL=(ALL) NOPASSWD:ALL" | sudo tee -a /etc/sudoers

# Activate a virtualenv. make will make it later. Also, set SHELL so mach
# doesn't complain while building Firefox. Other things probably expect it as
# well.
ENV VIRTUAL_ENV=/venv PATH=/venv/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin SHELL=/bin/bash

WORKDIR /home/dxr/dxr
USER dxr

EXPOSE 8000
CMD test -e /home/dxr/run && (chmod a+x /home/dxr/run && /home/dxr/run) || echo "No pre-defined command /home/dxr/run; exit container now."


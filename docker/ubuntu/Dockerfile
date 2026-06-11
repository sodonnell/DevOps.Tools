# ecomm-devops development container
#
# This docker image is intended to be used by the ecomm-devops team for managing an
# isolated workspace to modify and test scripts for the ecomm-devops repo and tool chain.
#
# Sean O'Donnell <sodonnell@visualcomfort.com>
#
FROM ubuntu:latest

# define your personal (non-root) user account criteria for the docker image
ENV USER=sodonnell
ENV USER_PASS=mypasswd
ENV HOME=/home/${USER}
ENV HOSTNAME=${USER}.local
ENV REPO_NAME="DevOps.Tools"
ENV REPO_PATH=git@github.com:sodonnell/${REPO_NAME}.git

# define the required/desired packages
ENV PACKAGES="\
    autoconf \
    automake \
    make \
    net-tools \
    git \
    less \
    libtool \
    dpkg-dev \
    dirmngr \
    btop \
    bzip2 \
    pkg-config \
    openssh-client \
    sudo \
    tmux \
    xz-utils \
    vim \
"

# install packages
RUN set -x && \
    apt update && \
    apt install -y --no-install-recommends ${PACKAGES}

# create/use personal (non-root) user
RUN useradd -rm -d ${HOME} -s /bin/bash -g root -G sudo -u 808 ${USER}
RUN echo "${USER}:${USER_PASS}" | chpasswd

# prepare your container workspace using your local/host SSH Keys to interact with github via ssh
RUN mkdir -p ${HOME}/.ssh && chmod 0700 ${HOME}/.ssh
RUN --mount=type=secret,id=ssh_prv_key cat /run/secrets/ssh_prv_key > ${HOME}/.ssh/id_rsa
RUN --mount=type=secret,id=ssh_pub_key cat /run/secrets/ssh_pub_key > ${HOME}/.ssh/id_rsa.pub
RUN chmod 600 ${HOME}/.ssh/id_rsa && chmod 600 ${HOME}/.ssh/id_rsa.pub
RUN ssh-keyscan github.com > ${HOME}/.ssh/known_hosts && chmod 0600 ${HOME}/.ssh/known_hosts
RUN chown -R ${USER}:root ${HOME}/.ssh

# clone the github repo using your local host keys
WORKDIR ${HOME}
USER ${USER}
RUN git clone ${REPO_PATH}
WORKDIR ${HOME}/${REPO_NAME}

# Run a bash shell by default
CMD ["bash"]

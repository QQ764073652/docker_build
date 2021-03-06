FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04
RUN APT_INSTALL="apt-get install -y --no-install-recommends" && \
    PIP_INSTALL="python -m pip --no-cache-dir install --upgrade" && \
    GIT_CLONE="git clone --depth 10" && \

    rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \

    apt-get update && \

# ==================================================================
# tools
# ------------------------------------------------------------------

    DEBIAN_FRONTEND=noninteractive $APT_INSTALL \
        build-essential \
        apt-utils \
        ca-certificates \
        openssh-client \
        openssh-server \
        cmake \
        wget \
        curl \
        git \
        vim \
        iputils-ping \
        net-tools \
        unzip \
        supervisor \
        && \

# ==================================================================
# python
# ------------------------------------------------------------------

    DEBIAN_FRONTEND=noninteractive $APT_INSTALL \
        software-properties-common \
        && \
    add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL \
        python3.6 \
        python3.6-dev \
        python3-distutils-extra \
        && \
    wget -O ~/get-pip.py \
        https://bootstrap.pypa.io/get-pip.py && \
    python3.6 ~/get-pip.py && \
    ln -s /usr/bin/python3.6 /usr/local/bin/python3 && \
    ln -s /usr/bin/python3.6 /usr/local/bin/python && \
    $PIP_INSTALL \
        setuptools \
        && \
    $PIP_INSTALL \
        numpy \
        scipy \
        pandas \
        cloudpickle \
        scikit-learn \
        matplotlib \
        Cython \
        futures tornado cssmin slimit psutil \
        && \

# ==================================================================
# jupyter
# ------------------------------------------------------------------

    $PIP_INSTALL \
        jupyter \
        && \

# ==================================================================
# tensorflow、tensorboard
# ------------------------------------------------------------------

    $PIP_INSTALL \
        tensorflow-gpu \
        tensorboard \
        && \
    mkdir -p /userhome/tensorboard/logs \
    && \

# ==================================================================
# gotaone webssh
# ------------------------------------------------------------------
    $PIP_INSTALL \
       tornado==4.5.1 \
       && \
    $GIT_CLONE https://github.com/liftoff/GateOne.git ~/GateOne && \
    cd ~/GateOne && python setup.py install \
    && \

# ==================================================================
# ssh
# https://10.10.8.5:23443?ssh=ssh://root@127.0.0.1
# ------------------------------------------------------------------
ssh-keygen -t rsa -b 4096 -P '' -f ~/.ssh/id_rsa >/dev/null 2>&1 && \
cat ~/.ssh/id_rsa.pub >~/.ssh/authorized_keys && \
mkdir -p /var/lib/gateone/users/ANONYMOUS/.ssh && \
echo id_rsa > /var/lib/gateone/users/ANONYMOUS/.ssh/.default_ids && \
cp ~/.ssh/id* /var/lib/gateone/users/ANONYMOUS/.ssh/ && \
sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
echo "StrictHostKeyChecking=no" > ~/.ssh/config && \
mkdir -p /var/log/supervisor && \

# ==================================================================
# config & cleanup
# ------------------------------------------------------------------

    ldconfig && \
    apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/*

COPY supervisord.conf /etc/supervisord.conf

EXPOSE 22 443 8888 6006

CMD ["/usr/bin/supervisord"]
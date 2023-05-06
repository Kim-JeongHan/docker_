FROM nvidia/cuda:11.7.1-cudnn8-devel-ubuntu20.04


ARG HOST_USER
ARG UNAME=${HOST_USER}
ARG UID=1000
ARG GID=1000
ARG HOME=/home/${UNAME}

# Remove any third-party apt sources to avoid issues with expiring keys.
RUN rm -f /etc/apt/sources.list.d/*.list

# Install some basic utilities & python prerequisites
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update -y && apt-get install -y --no-install-recommends\
    vim \
    curl \
    apt-utils \
    ssh \
    tree \
    ca-certificates \
    sudo \
    git \
    bzip2 \
    libx11-6 \
    make \
    build-essential \
    libssl-dev \
    zlib1g-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    wget \
    llvm \
    libncursesw5-dev \
    xz-utils \
    tk-dev \
    libxml2-dev \
    libxmlsec1-dev \
    libffi-dev \
    liblzma-dev \
    python3-openssl && \
    rm -rf /var/lib/apt/lists/*

# Set up time zone
RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8  
ENV LANGUAGE en_US:en  
ENV LC_ALL en_US.UTF-8  

# Add config for ssh connection
RUN echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config && \
    echo "PermitEmptyPasswords yes" >> /etc/ssh/sshd_config

RUN useradd -rm -d ${HOME} -s /bin/bash -g root -G sudo,audio,video,plugdev -u ${UID} ${UNAME}

# Create a non-root user and switch to it & Adding User to the sudoers File
RUN mkdir -p /etc/sudoers.d && \
    echo "${UNAME} ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/${UNAME} && \
    chmod 0440 /etc/sudoers.d/${UNAME} 
USER ${UNAME}

# All users can use /home/user as their home directory
ENV HOME /home/${UNAME}
RUN mkdir $HOME/.cache $HOME/.config && \
    chmod -R 777 $HOME 

# Create a workspace directory
RUN mkdir $HOME/workspace
WORKDIR $HOME/workspace

# Set up python environment with pyenv
ARG PYTHON_VERSION 3.10.6
RUN curl https://pyenv.run | bash
ENV PYENV_ROOT="$HOME/.pyenv"
ENV PATH "$PYENV_ROOT/bin:$PYENV_ROOT/shims:$PATH"
ENV eval "$(pyenv init -)"
RUN cd $HOME && /bin/bash -c "source .bashrc" && \
    /bin/bash -c "pyenv install -v $PYTHON_VERSION" && \
    /bin/bash -c "pyenv global $PYTHON_VERSION"

# Install Poetry
ENV PATH "$HOME/.local/bin:$PATH"
ENV PYTHON_KEYRING_BACKEND keyring.backends.null.Keyring
RUN curl -sSL https://install.python-poetry.org | python - && \
    poetry config virtualenvs.in-project true && \ 
    poetry config virtualenvs.path "./.venv"

# [option] Set up DL development environment (with poetry)
RUN mkdir $HOME/workspace/machine-learning
WORKDIR $HOME/workspace/machine-learning
COPY pyproject.toml $HOME/workspace/machine-learning/pyproject.toml
RUN /bin/bash -c "pyenv local $PYTHON_VERSION" && \
    poetry env use python3 && \
    poetry run poetry install --no-cache

COPY entrypoint.sh /
ENTRYPOINT ["/bin/bash","-c","/entrypoint.sh"]

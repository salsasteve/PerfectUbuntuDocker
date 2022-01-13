FROM ubuntu:latest

# RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# Configure apt
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
  apt-get install -y sudo \
  curl \
  git-core \
  gnupg \
  locales \
  zsh \
  wget \
  software-properties-common \
  vim \
  fonts-powerline \
  # set up locale
  && locale-gen en_US.UTF-8

RUN sudo add-apt-repository ppa:deadsnakes/ppa

ENV USER_NAME salsasteve
ENV USER_PASSWORD password

# add a user (--disabled-password: the user won't be able to use the account until the password is set)
RUN adduser --quiet --disabled-password --shell /bin/zsh --home /home/$USER_NAME --gecos "User" $USER_NAME
# update the password
RUN echo "${USER_NAME}:${USER_PASSWORD}" | chpasswd && usermod -aG sudo $USER_NAME
RUN echo $USER_NAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USER_NAME && chmod 0440 /etc/sudoers.d/$USER_NAME

# set home
ENV HOME /home/$USER_NAME

# generate locale for agnoster
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && /usr/sbin/locale-gen

# the user we're applying this too (otherwise it most likely install for root)
USER $USER_NAME

ENV TERM xterm

# Set the default shell to bash rather than sh
ENV SHELL /bin/zsh

# Install Python
RUN sudo apt install python3.8

# # Installing Node
# SHELL ["/bin/bash", "--login", "-i", "-c"]
# RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
# RUN source /root/.bashrc && nvm install --lts
# SHELL ["/bin/bash", "--login", "-c"]

ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION 16.13.2

WORKDIR $NVM_DIR

RUN curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default

ENV NODE_PATH $NVM_DIR/versions/node/v$NODE_VERSION/lib/node_modules
ENV PATH      $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

# run the installation script  
RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true

# install powerlevel10k
RUN git clone https://github.com/romkatv/powerlevel10k.git ~/.oh-my-zsh/custom/themes/powerlevel10k

RUN cd $HOME && curl -fsSLO https://raw.githubusercontent.com/romkatv/dotfiles-public/master/.purepower

# zsh configuration
ADD .zshrc $HOME
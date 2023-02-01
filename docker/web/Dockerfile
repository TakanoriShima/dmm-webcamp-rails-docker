FROM amazonlinux:2 as installer

# install awscli_v2.
RUN set -x \
    && yum install -y unzip \
        && curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
        && unzip awscliv2.zip \
        && ./aws/install -i /usr/local/aws-cli -b /usr/local/bin/aws

FROM amazonlinux:2
LABEL maintainer="<infratop>" \
        description="Amazon Linux 2 with some development environments"

# set package version.
ENV RUBY_VERSION="2.6.3" \
    PYTHON_VERSION="3.9.10" \
    TERRAFORM_VERSION="1.1.5" \
    ANSIBLE_VERSION="2.9.0" \
    USERNAME="ec2-user" \
    PASSWORD=""

# install packages.
RUN set -x \
    && yum update -y && yum install -y \
        bzip2 \
        bzip2-devel \
        curl \
        gcc-c++ \
        git \
        jq \
        less \
        libffi-devel \
        make \
        openssl \
        openssl-devel \
        perl-Digest-SHA \
        procps \
        readline \
        readline-devel \
        sqlite-devel \
        sudo \
        tar \
        mysql \
        unzip \
        wget \
        yum-utils \
        zlib-devel \
        https://prerelease.keybase.io/keybase_amd64.rpm \
    && amazon-linux-extras install epel -y \
    && yum install -y sshpass \
    && yum clean all \
    && rm -rf /var/cache/yum

    # add ec2-user.
RUN useradd -m -r -G wheel -s /bin/bash ${USERNAME} \
    && echo "${USERNAME}:${PASSWORD}" | chpasswd \
    && echo '%wheel ALL=(ALL) ALL' | EDITOR='tee -a' visudo \
    && echo "${USERNAME}   ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

COPY --from=installer /usr/local/aws-cli/ /usr/local/aws-cli/
COPY --from=installer /usr/local/bin/ /usr/local/bin/

USER ${USERNAME}
WORKDIR /home/${USERNAME}/environment

    # install tfenv.
RUN git clone https://github.com/tfutils/tfenv.git ~/.tfenv \
    && echo 'export PATH="$HOME/.tfenv/bin:$PATH"' | tee -a ~/.bash_profile \
    # install pyenv.
    && git clone https://github.com/pyenv/pyenv.git ~/.pyenv \
    && echo 'export PATH="$HOME/.pyenv/shims:$HOME/.pyenv/bin:$PATH"' | tee -a ~/.bash_profile \
    && echo 'eval "$(pyenv init -)"' | tee -a ~/.bash_profile \
    # install rbenv.
    && git clone https://github.com/sstephenson/rbenv.git ~/.rbenv \
    && git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build \
    && echo 'export PATH="$HOME/.rbenv/bin:$PATH"' | tee -a ~/.bash_profile \
    && echo 'eval "$(rbenv init -)"' | tee -a ~/.bash_profile \
    && echo 'export PATH="/usr/local/bin/aws:$PATH"' | tee -a ~/.bash_profile \
    && . ~/.bash_profile \
    # install Terraform.
    && tfenv install ${TERRAFORM_VERSION} \
    && tfenv use ${TERRAFORM_VERSION} \
    # install Python.
    && pyenv install ${PYTHON_VERSION} \
    && pyenv global ${PYTHON_VERSION} \
    # install Ruby.
    && rbenv install ${RUBY_VERSION} \
    && rbenv global ${RUBY_VERSION} \
    # upgrade pip.
    && pip3 install --upgrade pip \
    # install wheel.
    && pip3 install wheel \
    # install Ansible.
    && pip3 install ansible~=${ANSIBLE_VERSION} \
    # install node.
    && curl --silent --location https://rpm.nodesource.com/setup_16.x | sudo bash - \
    && sudo yum install -y nodejs \
    # install Rails 5.2.5.
    && gem install nokogiri -v 1.13.10 \
    && gem install rails -v 5.2.5 \
    # install ImageMagic 7.0.
    && sudo yum -y install libpng-devel libjpeg-devel libtiff-devel gcc \
    && git clone https://github.com/ImageMagick/ImageMagick.git ImageMagick-7.0.11 \
    && cd ImageMagick-7.0.11 \
    && ./configure \
    && make \
    && sudo make install \
    && cd .. \
    && sudo rm -rf ImageMagick-7.0.11



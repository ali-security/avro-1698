# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Dockerfile for installing the necessary dependencies for building Avro.
# See BUILD.txt.

# Despite this base, JDK8 is used by default.  See below.
FROM openjdk:11-slim-bullseye
WORKDIR /root

ENV APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=isolemnlysweariamuptonogood \
    DEBIAN_FRONTEND=noninteractive

# Install dependencies from vanilla system packages
RUN apt-get -qqy update
RUN apt-get -qqy install --no-install-recommends ant
RUN apt-get -qqy install --no-install-recommends apt-transport-https
RUN apt-get -qqy install --no-install-recommends apt-utils
RUN apt-get -qqy install --no-install-recommends asciidoc
RUN apt-get -qqy install --no-install-recommends bison
RUN apt-get -qqy install --no-install-recommends bzip2
RUN apt-get -qqy install --no-install-recommends bundler
RUN apt-get -qqy install --no-install-recommends cmake
RUN apt-get -qqy install --no-install-recommends cppcheck
RUN apt-get -qqy install --no-install-recommends curl
RUN apt-get -qqy install --no-install-recommends doxygen
RUN apt-get -qqy install --no-install-recommends flex
RUN apt-get -qqy install --no-install-recommends g++
RUN apt-get -qqy install --no-install-recommends gcc
RUN apt-get -qqy install --no-install-recommends git
RUN apt-get -qqy install --no-install-recommends libboost-all-dev
RUN apt-get -qqy install --no-install-recommends libfontconfig1-dev
RUN apt-get -qqy install --no-install-recommends libfreetype6-dev
RUN apt-get -qqy install --no-install-recommends libglib2.0-dev
RUN apt-get -qqy install --no-install-recommends libjansson-dev
RUN apt-get -qqy install --no-install-recommends libsnappy-dev
RUN apt-get -qqy install --no-install-recommends libsnappy1v5
RUN apt-get -qqy install --no-install-recommends make
RUN apt-get -qqy install --no-install-recommends maven
RUN apt-get -qqy install --no-install-recommends mypy
# RUN apt-get -qqy install --no-install-recommends openjdk-8-jdk
RUN apt-get -qqy install --no-install-recommends perl
RUN apt-get -qqy install --no-install-recommends python
RUN apt-get -qqy install --no-install-recommends python2
RUN apt-get -qqy install --no-install-recommends python3
RUN apt-get -qqy install --no-install-recommends rake
RUN apt-get -qqy install --no-install-recommends ruby
RUN apt-get -qqy install --no-install-recommends ruby-dev
RUN apt-get -qqy install --no-install-recommends source-highlight
RUN apt-get -qqy install --no-install-recommends subversion
RUN apt-get -qqy install --no-install-recommends valgrind
RUN apt-get -qqy install --no-install-recommends vim

RUN mkdir -p /opt/java/openjdk
RUN curl -L https://github.com/adoptium/temurin8-binaries/releases/download/jdk8u402-b06/OpenJDK8U-jdk_x64_linux_hotspot_8u402b06.tar.gz -o /tmp/jdk8.tar.gz
RUN tar -xzvf /tmp/jdk8.tar.gz -C /opt/java/openjdk
RUN ln -s /opt/java/openjdk/jdk8u402-b06 /usr/local/openjdk-8
RUN rm /tmp/jdk8.tar.gz
ENV JAVA_HOME=/usr/local/openjdk-8
ENV PATH=$JAVA_HOME/bin:$PATH

# Install nodejs 6
RUN curl -sSL https://deb.nodesource.com/setup_10.x -o /tmp/nodesource_setup.sh
RUN bash /tmp/nodesource_setup.sh
RUN apt-get -qqy install nodejs
RUN apt-get -qqy install npm
RUN apt-get -qqy clean
RUN rm -rf /var/lib/apt/lists
RUN npm install -g grunt-cli
RUN npm cache clean --force
RUN rm /tmp/nodesource_setup.sh

# Install PHP
ENV PHP7_VERSION=7.3
RUN curl -sSL https://packages.sury.org/php/apt.gpg \
  | apt-key add --no-tty - \
 && echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list \
 && apt-get -qqy update \
 && apt-get -qqy install --no-install-recommends libzstd-dev \
                                                 libbz2-dev \
                                                 php${PHP7_VERSION} \
                                                 php${PHP7_VERSION}-bz2 \
                                                 php${PHP7_VERSION}-gmp \
                                                 php${PHP7_VERSION}-xml \
                                                 php${PHP7_VERSION}-mbstring \
                                                 php${PHP7_VERSION}-dev \
 && mkdir tmp && cd tmp \
 && git clone --recursive --depth=1 https://github.com/kjdev/php-ext-zstd.git \
 && cd php-ext-zstd \
 && phpize \
 && ./configure \
 && make \
 && make install \
 && echo "extension=zstd.so" > /etc/php/7.3/cli/conf.d/10-zstd.ini \
 && cd .. && rm -rf php-ext-zstd \
 && git clone --recursive --depth=1 https://github.com/kjdev/php-ext-snappy.git \
 && cd php-ext-snappy \
 && phpize \
 && ./configure \
 && make \
 && make install \
 && echo "extension=snappy.so" > /etc/php/7.3/cli/conf.d/10-snappy.ini \
 && cd .. && rm -rf php-ext-snappy \
 && php -m \
 && apt-get -qqy clean \
 && rm -rf /var/lib/apt/lists

RUN curl -sS https://getcomposer.org/installer | php -- --version=2.0.7 --install-dir=/usr/local/bin --filename=composer

# Install Perl modules
RUN apt-get -qqy update \
 && apt-get -qqy install --no-install-recommends libcompress-raw-zlib-perl \
                                                 libcpan-uploader-perl \
                                                 libencode-perl \
                                                 libio-string-perl \
                                                 libjson-xs-perl \
                                                 libmodule-install-perl \
                                                 libmodule-install-readmefrompod-perl \
                                                 libobject-tiny-perl \
                                                 libperl-critic-perl \
                                                 libregexp-common-perl \
                                                 libtest-exception-perl \
                                                 libtest-pod-perl \
                                                 libtry-tiny-perl \
 && apt-get -qqy clean \
 && rm -rf /var/lib/apt/lists
RUN curl -sSL https://cpanmin.us \
  | perl - --self-upgrade \
 && cpanm --mirror https://www.cpan.org/ install Compress::Zstd \
                                                 Module::Install::Repository \
 && rm -rf .cpanm

# Install Python packages
ENV PIP_NO_CACHE_DIR=off

# Install Python2 packages
RUN curl -sS https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
RUN python2 get-pip.py
RUN rm get-pip.py
RUN python2 -m pip install --upgrade pip setuptools wheel
RUN python2 -m pip install zstandard

# Install Python3 packages
RUN apt-get -qqy update
RUN apt-get -qqy install python3-pip
RUN python3 -m pip install --upgrade pip setuptools wheel
RUN python3 -m pip install tox zstandard

# Install .NET SDK
RUN curl -sSLO https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb \
 && dpkg -i packages-microsoft-prod.deb \
 && rm packages-microsoft-prod.deb \
 && apt-get -qqy update \
 && apt-get -qqy install --no-install-recommends dotnet-sdk-3.1 \
 && apt-get -qqy clean \
 && rm -rf /var/lib/apt/lists

# Install Ruby modules
COPY lang/ruby/Gemfile /tmp
RUN bundle install --gemfile=/tmp/Gemfile

# Note: This "ubertool" container has two JDK versions:
# - OpenJDK 8 (installed as a package and available at /usr/bin/java)
# - OpenJDK 11 (installed from the base image, and prioritized in the PATH)
# - The root build.sh script switches between the versions according to
#   the JAVA environment variable.

# Since want the JDK8 as a default, we have to re-prepend it to the PATH.
# The /usr/local/openjdk-8 link is created to be consistent with the
# openjdk:8 image.

CMD ["/bin/bash", "-i"]

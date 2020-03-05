# Use phusion/baseimage as base image. To make your builds
# reproducible, make sure you lock down to a specific version, not
# to `latest`! See
# https://github.com/phusion/baseimage-docker/blob/master/Changelog.md
# for a list of version numbers.
FROM phusion/baseimage

ARG DEBIAN_FRONTEND=noninteractive

EXPOSE 222

# Use baseimage-docker's init system.
CMD ["/sbin/my_init"]

# ...put your own build instructions here...

# Clean up APT when done.
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN rm -f /etc/service/sshd/down

## Install an SSH of your choice.
COPY .ssh/id_rsa.pub /tmp/id_rsa.pub
RUN cat /tmp/id_rsa.pub >> /root/.ssh/authorized_keys && rm -f /tmp/id_rsa.pub

COPY ssh/sshd_config /etc/ssh/sshd_config

##Install compiler for Visual Studio
RUN curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
RUN install -o root -g root -m 644 packages.microsoft.gpg /usr/share/keyrings/
RUN sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'

RUN apt-get install -y apt-transport-https
RUN apt-get update
RUN apt-get install -y code # or code-insiders
RUN apt-get install -y g++ gdb gdbserver
RUN apt-get install -y rsync

##Install MySql Libraries
RUN apt-get install -y libmysqlcppconn-dev

##Install Redis Libraries
RUN apt-get install -y libhiredis-dev

##Install Aws Sdk Libraries
RUN apt-get install -y git cmake
RUN apt-get install -y libcurl4-openssl-dev libssl-dev uuid-dev zlib1g-dev libpulse-dev

RUN cd /tmp && git clone https://github.com/aws/aws-sdk-cpp.git
RUN cd /tmp/aws-sdk-cpp && cmake -D CMAKE_BUILD_TYPE=Release .
RUN cd /tmp/aws-sdk-cpp && make -j 8 && make install && ldconfig

##Install seznam/elasticlient Library
RUN apt-get install -y libmicrohttpd-dev libssh2-1

##Finally, make seznam/elasticlient Library
RUN cd /tmp && git clone https://github.com/seznam/elasticlient.git
RUN cd /tmp/elasticlient && git submodule update --init --recursive
RUN cd /tmp/elasticlient && mkdir build
RUN cd /tmp/elasticlient/build && cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_ELASTICLIENT_EXAMPLE=NO -DBUILD_ELASTICLIENT_TESTS=NO -DUSE_SYSTEM_CURL=ON ..
RUN cd /tmp/elasticlient/build && make -j 8 && make install && ldconfig

RUN cp -R /tmp/elasticlient/build/lib/* /usr/local/lib
RUN cp -R /tmp/elasticlient/external/cpr/include/* /usr/local/include

RUN ldconfig
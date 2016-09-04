FROM debian:jessie

MAINTAINER keopx <keopx@keopx.net>

#
# Step 1: Installation
#

# Set frontend. We'll clean this later on!
ENV DEBIAN_FRONTEND noninteractive

# Set repositories
RUN \
  echo "deb http://ftp.de.debian.org/debian/ jessie main non-free contrib\n" > /etc/apt/sources.list && \
  echo "deb-src http://ftp.de.debian.org/debian/ jessie main non-free contrib\n" >> /etc/apt/sources.list && \
  echo "deb http://security.debian.org/ jessie/updates main contrib non-free\n" >> /etc/apt/sources.list && \
  echo "deb-src http://security.debian.org/ jessie/updates main contrib non-free" >> /etc/apt/sources.list
  
# Update repositories cache and distribution
RUN apt-get -qq update && apt-get -qqy upgrade

# Install some basic tools needed for deployment
RUN apt-get -yqq install --no-install-recommends \
  curl \
  python

# Configure Varnish-cache sources
RUN \
  curl http://repo.varnish-cache.org/GPG-key.txt | apt-key add -- && \
  echo "deb http://repo.varnish-cache.org/debian/ jessie varnish-4.0" >> /etc/apt/sources.list.d/varnish-cache.list && \
  apt-get -qq update

# Install Varnish-cache
RUN apt-get -yqq install \
  varnish --no-install-recommends
  
#
# Step 3: Clean the system
#

# Cleanup some things
RUN apt-get -q autoclean && \
  rm -rf /var/lib/apt/lists/*

#
# Step 4: Run
#

COPY config/default.vcl /etc/varnish/default.vcl

ENV VARNISH_BACKEND_PORT 80
ENV VARNISH_BACKEND_IP 172.17.42.1
ENV VARNISH_PORT 80
ENV VARNISH_MEMORY 100M

EXPOSE 80 6082

COPY scripts/varnish /usr/local/bin/start-varnishd

CMD ["start-varnishd"]
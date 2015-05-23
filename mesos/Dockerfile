FROM ubuntu:15.04
MAINTAINER Cleawing Inception <inception@cleawing.com>

RUN echo "deb http://repos.mesosphere.io/ubuntu/ trusty main" > /etc/apt/sources.list.d/mesosphere.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv DF7D54CBE56151BF
RUN apt-get -y update
RUN apt-get -y install mesos=0.22.1-1.0.ubuntu1404
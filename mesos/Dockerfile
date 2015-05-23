FROM ubuntu:14.04.2
MAINTAINER Cleawing Inception <inception@cleawing.com>

ENV DEBIAN_FRONTEND noninteractive

RUN DISTRO=$(lsb_release -is | tr '[:upper:]' '[:lower:]'); CODENAME=$(lsb_release -cs); echo "deb http://repos.mesosphere.io/${DISTRO} ${CODENAME} main" | tee /etc/apt/sources.list.d/mesosphere.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv E56151BF
RUN	apt-get -q update
RUN	apt-get -y -q install mesos=0.22.1-1.0.ubuntu1404
RUN apt-get -y -q remove zookeeper zookeeperd zookeeper-bin
RUN apt-get autoremove -y -q
RUN apt-get clean -y -q
#
#  Author: Hari Sekhon
#  Date: 2016-01-16 09:58:07 +0000 (Sat, 16 Jan 2016)
#
#  vim:ts=4:sts=4:sw=4:et
#
#  https://github.com/harisekhon/Dockerfiles
#
#  If you're using my code you're welcome to connect with me on LinkedIn and optionally send me feedback to help improve or steer this or other code I publish
#
#  https://www.linkedin.com/in/harisekhon
#

FROM ubuntu:latest
MAINTAINER Hari Sekhon (https://www.linkedin.com/in/harisekhon)

LABEL Description="Advanced Nagios Plugins Collection (on Ubuntu)"

ENV DEBIAN_FRONTEND noninteractive

ENV PATH $PATH:/github/nagios-plugins

RUN mkdir -v /github

WORKDIR /github

# drops in to /bin/sh pushd not available, could bash -c but explicit paths are good enough
RUN bash -c ' \
    set -euxo pipefail && \
    x=nagios-plugins && \
    apt-get update && \
    apt-get install -y git make && \
    # just for check_puppet.rb, but it doesn't make sense to use that plugin in a container as it's a local style plugin
    #apt-get install -y ruby && \
    git clone https://github.com/harisekhon/$x /github/$x && \
    cd /github/$x && \
    make build zookeeper && \
    make apt-packages-remove && \
    apt-get autoremove -y && \
    apt-get clean && \
    # run tests after autoremove to check that no important packages we need get removed
    make test deep-clean && \
    # leave git it's needed for Git-Python and check_git_branch_checkout.pl/py
    apt-get remove -y make && \
    apt-get autoremove -y && \
    apt-get clean && \
    bash-tools/check_docker_clean.sh && \
    # basic test for missing dependencies again
    tests/help.sh \
    '

WORKDIR /github/nagios-plugins

# trying to do -exec basename {} \; results in only the jython files being printed
CMD /bin/bash -c "find /github/nagios-plugins -maxdepth 2 -type f -iname '[A-Za-z]*.pl' -o -iname '[A-Za-z]*.py' | grep -iv makefile | xargs -n1 basename | sort"

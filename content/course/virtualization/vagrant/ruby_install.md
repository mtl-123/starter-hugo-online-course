---
title: Ruby 解释器的安装
date: '2022-10-31'
type: book
weight: 20
---

# Ruby Shell 安装
```bash
tee >> ./ruby-scl.sh <'EOF'
#!/bin/sh

#==============================================================================
#title            : ./ruby-scl.sh
#description      : This script will install/configure Ruby and Inspect
#author   		  : MeiChen
#date             : 10/31/2022
#version          : 0.1
#usage            : ./ruby-scl.sh
#notes            : Script requires Unix shell
#ref              :
#                 https://computingforgeeks.com/install-ruby-on-rails-on-rocky-almalinux/
#==============================================================================

echo "Executing [$0]..."

# Stop script on any error
set -e

#################################################################
# Check if run as root
#################################################################
if [ ! "$(id -u)" -eq 0 ]; then
	echo "ERROR: Script [$0] must be run as root, Script terminating"
	exit 7
fi
#################################################################

sudo dnf update -y
sudo yum install -y git gnupg2 git-core zlib zlib-devel gcc-c++ patch readline readline-devel libffi-devel openssl-devel make bzip2 autoconf automake libtool bison curl tar sqlite-devel
sudo dnf install ruby ruby-devel -y
ruby --version

curl -sSL https://rvm.io/pkuczynski.asc | gpg2 --import -
curl -sSL https://rvm.io/mpapis.asc | gpg2 --import -
sudo dnf -y install tar
curl -sSL https://get.rvm.io | bash -s stable

source /etc/profile.d/rvm.sh

rvm reload

rvm list known

rvm install ruby 1.8.6

sudo yum install git -y

git clone https://github.com/rbenv/rbenv.git .rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(rbenv init -)"' >> ~/.bash_profile

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bash_profile
source ~/.bash_profile

rbenv install -l

rbenv install -v 3.1.2

rbenv rehash

echo Done!

echo "Executing [$0] complete"
EOF
```

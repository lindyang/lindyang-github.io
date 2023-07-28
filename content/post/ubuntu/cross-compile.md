---
title: "Cross Compile"
date: 2023-07-28T14:24:03+08:00
tags: [ubuntu]
---


```
USER=lindyang
DIRECTORY=/opt/arm64

sudo apt install -y debootstrap qemu-user-static binfmt-support schroot
sudo debootstrap --arch=arm64 --foreign --include=gcc,g++ bionic $DIRECTORY
sudo cp /usr/bin/qemu-aarch64-static $DIRECTORY/usr/bin

cat <<EOF | sudo tee /etc/schroot/chroot.d/bionic-arm64
[bionic-arm64]
description=Ubuntu bionic arm64
type=directory
directory=$DIRECTORY
groups=sbuild,root
root-groups=sbuild,root
users=root,$USER
EOF

sudo chroot $DIRECTORY
/debootstrap/debootstrap --second-stage
export LANG=en_US.UTF-8
cp /etc/apt/sources.list{,.orig}
cat > /etc/apt/sources.list <<'EOF'
deb http://ports.ubuntu.com/ubuntu-ports bionic main restricted
deb http://ports.ubuntu.com/ubuntu-ports bionic-updates main restricted
deb http://ports.ubuntu.com/ubuntu-ports bionic universe
deb http://ports.ubuntu.com/ubuntu-ports bionic-updates universe
deb http://ports.ubuntu.com/ubuntu-ports bionic multiverse
deb http://ports.ubuntu.com/ubuntu-ports bionic-updates multiverse
deb http://ports.ubuntu.com/ubuntu-ports bionic-backports main restricted universe multiverse
deb http://ports.ubuntu.com/ubuntu-ports bionic-security main restricted
deb http://ports.ubuntu.com/ubuntu-ports bionic-security universe
deb http://ports.ubuntu.com/ubuntu-ports bionic-security multiverse
EOF

apt update;
apt install -y git curl;

mkdir -p ~;
git clone https://gitee.com/pyenv/pyenv.git ~/.pyenv;
cat >> ~/.bashrc <<'EOF'
export PATH="~/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
EOF
source ~/.bashrc;

mkdir ~/.pip;
cat > ~/.pip/pip.conf <<EOF
[global]
index-url=https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-url=pypi.tuna.tsinghua.edu.cn
EOF

apt install -y libmysql++-dev;
apt install -y libmysqlclient-dev;  # mysql_config
apt install -y build-essential;  # gcc
apt install -y libtool;  # gcc
apt install -y libreadline6-dev;  # readline
apt install -y libbz2-dev;  # bz2
apt install -y libsqlite3-dev;  # SQLite3
apt install -y zlib1g-dev;  # zlib
apt install -y libssl-dev;  # ssl
apt install -y libffi-dev;  # No module named ‘_ctypes’
apt install -y libncursesw5-dev;  # No module named '_curses'

mkdir ~/.pyenv/cache;
PYV=3.7.3;
curl -OC - https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tar.xz;
mv Python-3.7.3.tar.xz ~/.pyenv/cache/;
pyenv install -v $PYV;
```


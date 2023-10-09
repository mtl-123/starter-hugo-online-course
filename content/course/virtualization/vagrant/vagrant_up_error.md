---
title: Vagrant 报错
date: '2022-10-17'
type: book
weight: 20
---


在ubuntu系统上执行vagrant up 报错如下

```bash
root@m:~# vagrant up
No usable default provider could be found for your system.

Vagrant relies on interactions with 3rd party systems, known as
"providers", to provide Vagrant with resources to run development
environments. Examples are VirtualBox, VMware, Hyper-V.

The easiest solution to this message is to install VirtualBox, which
is available for free on all major platforms.

If you believe you already have a provider available, make sure it
is properly installed and configured. You can see more details about
why a particular provider isn't working by forcing usage with
`vagrant up --provider=PROVIDER`, which should give you a more specific
error message for that particular provider.

```

可使用 **`vagrant up --provider virtualbox`** 查看出错原因
下面说 当前的 VirtualBox 版本不支持 vagrant 下面已经把支持的版本号列出了，
在VirtualBox官网下载对应的版本即可

```bash
root@m:~# vagrant up --provider virtualbox
The provider 'virtualbox' that was requested to back the machine
'default' is reporting that it isn't usable on this system. The
reason is shown below:

Vagrant has detected that you have a version of VirtualBox installed
that is not supported by this version of Vagrant. Please install one of
the supported versions listed below to use Vagrant:

4.0, 4.1, 4.2, 4.3, 5.0, 5.1, 5.2, 6.0, 6.1

A Vagrant update may also be available that adds support for the version
you specified. Please check www.vagrantup.com/downloads.html to download
the latest version.

```

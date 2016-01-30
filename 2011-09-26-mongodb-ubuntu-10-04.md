---
layout: post
title:  Instalando o MongoDB no Ubuntu 10.04
date:   2011-09-26 02:18:11
---
Primeiro, adicione a chave GPG do repositório do MongoDB:

```sh
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv 7F0CEB10
```

Edite o arquivo _/etc/apt/sources.list_ e adicione o repositório do MongoDB:

```text
deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen
```

Atualize a lista de pacotes:

```sh
sudo apt-get update
```

E instale o MongoDB:

```sh
sudo apt-get install mongodb-10gen
```

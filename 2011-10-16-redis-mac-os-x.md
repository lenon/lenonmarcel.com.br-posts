---
layout: post
title:  Instalando o Redis no Mac OS X
date:   2011-10-16 01:00:14
---
Faça o download do source, _unpack_ e compile:

    curl -o redis-2.4.0.tar.gz http://redis.googlecode.com/files/redis-2.4.0.tar.gz
    tar xzf redis-2.4.0.tar.gz
    cd redis-2.40.
    make

Opcionalmente, você pode rodar os testes do Redis:

    make test

Se tudo estiver ok, `\o/ All tests passed without errors!` deverá aparecer após a suite de testes rodar.

Para _startar_ o Redis server:

    ./src/redis-server

:)

---
layout: post
title: Configurando Path Stack
---
Dada uma limitação de espaço que tenho em um notebook preciso sempre apontar o Stack para outra unidade de disco.
No caso uma instalação Windows.

Felizmente só precisa apontar a variável de ambiente ```STACK_ROOT``` para o novo destino.
```sh
STACK_ROOT = D:\NovoPath
```

[Referência https://docs.haskellstack.org/en/stable/README/](https://docs.haskellstack.org/en/stable/README/)

---
layout: post
title: Docker -> Portainer -> Zeppelin
---

## Instalando o docker
```
sudo apt install docker.io
```
Isso por si só já deve criar um usergroup docker e colocar o usuário atual nele. Mas ao tentar rodar pode aparecer urescuem erro de permissão. Pois o mecanismo o qual adicionar o usuário ao grupo docker garante permissão para rodar o docker com acesso ao socket do docket no ```/var/run/docker.sock```. Se o sistema de arquivos que contêm ```/var/run``` foi montado com ACLs ativo (meu caso), isso deve ser feito via ACLs.
```
sudo setfacl -m user:$USER:rw /var/run/docker.sock
```

## Alterando a pasta padrão do docker (Opcional)

Como desejo utilizar o docker, como base para testes de desenvolvimento, e sei que precisarei de muito espaço em disco para tal. Vou alterar a pasta padrão para que utilize outro caminho dedicado somente a isso. Podendo ser uma unidade de disco separada por exemplo.

Criando a unidade
```
mkdir /docker
mkdir /docker/docker_var
```

Criando a configuração personalizada
```
sudo mkdir -p /etc/systemd/system/docker.service.d 
sudo nano /etc/systemd/system/docker.service.d/docker-storage.conf
```

Para docker versões anteriores a 17.06-ce cole:
```
[Service] 
ExecStart= 
ExecStart=/usr/bin/docker daemon -H fd:// --graph="/docker/docker_var"
``` 
para o docker em versões posteriores a 17.06-ce cole:
```
[Service] 
ExecStart= 
ExecStart=/usr/bin/dockerd -H fd:// --data-root="/docker/docker_var"
```

Reinicie os serviços para ver se a configuração funcionou
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

Para confirmar a configuração:
```
docker info|grep "loop file"
```
Saida esperada:
``` 
Data loop file: /docker/docker_var/devicemapper/devicemapper/data 
Metadata loop file: /docker/docker_var/devicemapper/devicemapper/metadata
```

Em versões recente (17.03) o comando é diferente:
```
docker info|grep "Docker Root Dir"
```
Saida esperada:
```
Docker Root Dir: /docker/docker_var
```

Agora você pode apagar de forma segura o armazenamento antigo do Docker:
```
rm -rf /var/lib/docker
```

## [Portainer](https://www.portainer.io/), gerenciando os containers 
Para facilitar o gerenciamento dos containter, utiliso o portainer
Instalando:
```
docker volume create portainer_data
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

Ao acessar a primeira vez o localhost:9000 irá lhe solicitar a criação de usuário e senha.
Agora já pode fazer grande parte do gerenciamento diretamente da interface web.


## Instalando o [Zeppelin](https://zeppelin.apache.org/)
```
docker run -p 8080:8080 --rm -v $PWD/logs:/logs -v $PWD/notebook:/notebook -e ZEPPELIN_LOG_DIR='/logs' -e ZEPPELIN_NOTEBOOK_DIR='/notebook' --name zeppelin apache/zeppelin:0.8.0Objetivo é ter uma instalação funcional e gerenciável do Zeppelin no ubuntu.
```













# Portainer Deployer

O [Portainer](https://www.portainer.io) é um poderoso gerenciador de containers por trás de uma fácil interface de usuário (UI). Ele permite gerenciar e construir containers através de uma interface Web, além de permitir gerenciar containers que estão em outros dispositivos, Swarm e Kubernetes. Documentação do projeto em: https://docs.portainer.io.

## Sumário

- [Portainer Deployer](#portainer-deployer)
  - [Sumário](#sumário)
  - [Requisitos e Dependências:](#requisitos-e-dependências)
  - [Instalação](#instalação)
    - [Estrutura de Diretórios](#estrutura-de-diretórios)
    - [Docker-Compose](#docker-compose)
      - [Portas](#portas)
      - [Volumes](#volumes)
    - [Docker-Compose - Rede](#docker-compose---rede)


## Requisitos e Dependências:
- [Docker e Docker-Compose](https://docs.docker.com/)

## Instalação

### Estrutura de Diretórios

```yml
# Crie os diretórios

# Dir. config/dados
$ mkdir $(pwd)/lib_data
```

Sugestão (no Linux):
- Dir. config/dados: */var/lib/portainer*

### Docker-Compose

#### Portas

```yml
# docker-compose.yml (Em services.app)
# Descomente (e/ou altere) as portas/serviços que você deseja oferecer.

ports:
# Porta de túnel - necessário apenas em caso de uso de agentes de borda.
  - '8000:8000'
# Porta para a interface Web de administração - HTTP.
  - '9000:9000'
# Porta para a interface Web de administração - HTTPS.
  - '9443:9443'
```

#### Volumes

```yml
# docker-compose.yml (Em services.app)
# Aponte para as pastas criadas anteriormente.

# Antes
volumes:
  - '$(pwd)/docker.sock:/var/run/docker.sock'
  - '$(pwd)/var_lib_data:/data'

# Depois (exemplo)
volumes:
  - '/var/run/docker.sock:/var/run/docker.sock'
  - '/var/lib/portainer:/data'
```

Obs.: altere "$(pwd)/docker.sock" pelo caminho do Docker RUN em seu sistema operacional. No Linux, geralmente, */var/run/docker.sock*.

### Docker-Compose - Rede

```yml
# docker-compose.yml (Em networks.portainer-net.ipam)
# Altere o valores caso necessário. 

config:
# Endereço da rede
  - subnet: '172.18.0.0/28'
```

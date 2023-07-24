# Portainer Deployer

O [Portainer](https://www.portainer.io) é um poderoso gerenciador de containers por trás de uma fácil interface de usuário (UI). Ele permite gerenciar e construir containers através de uma interface Web, além de permitir gerenciar containers que estão em outros dispositivos, Swarm e Kubernetes. Documentação do projeto em: https://docs.portainer.io.

## Sumário

- [Portainer Deployer](#portainer-deployer)
  - [Sumário](#sumário)
  - [1. Requisitos e Dependências:](#1-requisitos-e-dependências)
  - [2. Portainer Agent - Modo Passivo](#2-portainer-agent---modo-passivo)
    - [2.1. Portas](#21-portas)
    - [2.2. Volumes](#22-volumes)
    - [2.3. Rede](#23-rede)
  - [3. Portainer Agent - Modo Ativo (Edge Agent)](#3-portainer-agent---modo-ativo-edge-agent)
    - [3.1. Volumes](#31-volumes)
    - [3.2. Variáveis de Ambiente (Environment)](#32-variáveis-de-ambiente-environment)
    - [3.3. Rede](#33-rede)
  - [4. Portainer (Manager)](#4-portainer-manager)
    - [4.1. Portas](#41-portas)
    - [4.2. Volumes](#42-volumes)
    - [4.3. Rede](#43-rede)


## 1. Requisitos e Dependências:
- [Docker e Docker-Compose](https://docs.docker.com/)

## 2. Portainer Agent - Modo Passivo

### 2.1. Portas

```yml
# stack-agent.docker-compose.yml

# Em "services.app".
# Comente/Descomente (e/ou altere) as portas/serviços que você deseja prover.

ports:
# Porta de acesso ao agente.
  - '9001:9001'
```

### 2.2. Volumes

```yml
# stack-agent.docker-compose.yml

# Em "services.app".
# Aponte para os locais corretos.

volumes:
# Socket do "Docker Daemon".
  - $(pwd)/docker.sock:/var/run/docker.sock
# Pasta dos volumes docker.
  - $(pwd)/docker_volumes:/var/lib/docker/volumes
```

### 2.3. Rede

```yml
# stack-agent.docker-compose.yml

# Em "networks.portainer-agent-net.ipam".
# Altere o valores caso necessário. 

config:
# Endereço da rede.
  - subnet: 10.0.1.0/28
```

## 3. Portainer Agent - Modo Ativo (Edge Agent)

### 3.1. Volumes

```yml
# stack-edge-agent.docker-compose.yml

# Em "services.app".
# Aponte para os locais corretos.

volumes:
# Socket do "Docker Daemon".
  - $(pwd)/docker.sock:/var/run/docker.sock
# Pasta dos volumes docker.
  - $(pwd)/docker_volumes:/var/lib/docker/volumes
# Aponta para o diretório "root" do host, comente caso não necessite.
  - /:/host
# Local para armazenar os dados de acesso do Portainer.
  - $(pwd)/lib_data:/data
```

### 3.2. Variáveis de Ambiente (Environment)

```yml
# stack-edge-agent.docker-compose.yml

# Em "services.app".

environment:
# Habilita o modo inseguro para acesso do Portainer.
  - EDGE_INSECURE_POLL=1
# Habilita o modo "Edge".
  - EDGE=1
# ID gerado pelo Portainer.
  - EDGE_ID=
# Key gerado pelo Portainer.
  - EDGE_KEY=
# Nome do service, requerido somento e modo swarm.
  - AGENT_CLUSTER_ADDR=tasks.portainer-stack_app
```

### 3.3. Rede

```yml
# stack-edge-agent.docker-compose.yml

# Em "networks.portainer-agent-net.ipam".
# Altere o valores caso necessário. 

config:
# Endereço da rede.
  - subnet: 10.0.1.0/28
```

## 4. Portainer (Manager)

Os arquivos de *build* do Portainer são apresentado de duas formas mutuamente excludentes: (1) método comum através do arquivo [*docker-compose.yml*](./docker-compose.yml); (2) stack, para uso em modo Swarm, arquivo [*stack-portainer-ce.docker-compose.yml*](./stack-portainer-ce.docker-compose.yml).

### 4.1. Portas

```yml
# (docker-compose|stack-portainer-ce.docker-compose).yml 

# Em services.app
# Descomente (e/ou altere) as portas/serviços que você deseja oferecer.

ports:
# Porta de túnel - necessário apenas em caso de uso de agentes em modo Edge.
  - '8000:8000'
# Porta para a interface Web de administração - HTTP.
  - '9000:9000'
# Porta para a interface Web de administração - HTTPS.
  - '9443:9443'
```

### 4.2. Volumes

```yml
# (docker-compose|stack-portainer-ce.docker-compose).yml 

# Em "services.app".
# Aponte para os locais corretos.

volumes:
# Socket do "Docker Daemon".
  - $(pwd)/docker.sock:/var/run/docker.sock
# Local para armazenar os dados do Portainer.
  - $(pwd)/lib_data:/data
```

### 4.3. Rede

```yml
# (docker-compose|stack-portainer-ce.docker-compose).yml

# Em networks.portainer-net.ipam
# Altere o valores caso necessário. 

config:
# Endereço da rede
  - subnet: 172.18.0.0/28
```

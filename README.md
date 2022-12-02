# Portainer Deploy

## Sumário

- [Portainer Deploy](#portainer-deploy)
  - [Sumário](#sumário)
  - [Sobre](#sobre)
    - [Requisitos e Dependências:](#requisitos-e-dependências)
  - [Instalação](#instalação)
    - [Configuração de Parâmetros - docker-compose](#configuração-de-parâmetros---docker-compose)
      - [**Portas**](#portas)
      - [**Volumes**](#volumes)
      - [**Certificado**](#certificado)
  - [Geração de Certificado Digital (HTTPS)](#geração-de-certificado-digital-https)

## Sobre

O Portainer é um poderoso gerenciador de containers por trás de uma fácil interface de usuário (UI). Ele permite gerenciar e construir containers através de uma interface Web, além de permitir gerenciar containers que estão em outros dispositivos, Swarm e Kubernetes.

- Site: https://www.portainer.io/
- Docs: https://docs.portainer.io/

### Requisitos e Dependências:
- [Docker e Docker-Compose](https://docs.docker.com/)
- [OpenSSL - Opcional](https://www.openssl.org/)
  
## Instalação

### Configuração de Parâmetros - docker-compose
<br>

**Obs.1**: comente/descomente para habilitar/desabilitar.

#### **Portas**

```yml
# docker-compose.yml

ports:
  # - "0.0.0.0:8000:8000" 
  - "0.0.0.0:9443:9443"
  # - "0.0.0.0:9000:9000"
```

1. **[IP:8000:8000]**: expõe um servidor de túnel TCP. **Opcional** - necessário apenas em caso de uso de agentes de borda.
2. **[IP:9443:9443]**: expõe a interface (Web) de usuário via *HTTPS*. (**Recomendado**)
3. **[IP:9000:9000]**: expõe a interface (Web) de usuário via *HTTP*. (**Não recomendado**).

**Obs.2**: IP pode assumir diversos valores, tais como: (1) "0.0.0.0" para "ouvir" em todas as interfaces do host; (2) "127.0.0.1" para apenas localhost; (3) IP do host e etc. Para mais detalhes consulte a documentação do Docker.

#### **Volumes**
```yml
# docker-compose.yml

volumes:
  - /hostPath/docker.sock:/var/run/docker.sock # Volume_1
  - /hostPath/data:/data # Volume_2
  #  - /hostPath/letsencrypt/live/yourdomain:/certs/live/app:ro # Volume_3
  #  - /hostPath/letsencrypt/archive/yourdomain:/certs/archive/app:ro # Volume_4
```
1. **Volume_1**: altere "/hostPath/docker.sock" pelo caminho do Docker RUN em seu sistema operacional. No Linux, geralmente, "/var/run/docker.sock".

2. **Volume_2**: altere "/hostPath/data" para o local de salvamento de dados da aplicação.

3. **Volume_3**: altere "/hostPath/letsencrypt/live/yourdomain" para o local onde está armazenado os certificado digital ("\*nome\*.pem") e a chave privada ("\*nome\*.key"). (**Recomendado**) | [Instrução para geração de certificados.](#geração-de-certificado-digital-https). Caso não definido será gerado na inicialização do container automaticamente.

4. **Volume_4**: altere "/hostPath/letsencrypt/archive/yourdomain" para o local de armazenamento de outros dados dados relacionados ao certificado. (**Opcional**)

#### **Certificado** 

Caso tenha optado por gerar seu próprio certificado TLS/SSL para conexão HTTPS configure:

```yml
# docker-compose.yml

# Descomente: antes
# command: --sslcert /certs/live/app/portainer_cert.pem --sslkey /certs/live/app/portainer_priv.key

# Depois
command: --sslcert /certs/live/app/portainer_cert.pem --sslkey /certs/live/app/portainer_priv.key
```

**Obs.1**: troque "portainer_cert.pem" e "portainer_priv.key", respectivamente, pelos arquivos de certificado e de chave privada que você gerou.

## Geração de Certificado Digital (HTTPS)

```bash
# Após instalar o OpenSSL

# Geração de chave privada RSA
$ openssl genpkey -out $value_1 -algorithm RSA -pkeyopt rsa_keygen_bits:$value_2

# Geração de certificado
$ openssl req -new -x509 -key $value_1 -out $value_3 -days $value_4 -subj="/C=$value_5/ST=$value_6/L=$value_7/O=$value_8/OU=$value_9/CN=$value_10/emailAddress=$value_11"
```
Valores (substitua):
1. \$value_1: nome do arquivo para guardar a chave privada. Ex.: "\*nome\*.key".
2. \$value_2: quantidade de bits da chave gerada. Ex.: 2048 (recomendado), 3072 (Ideal), 4096 (Forte - porém mais lento).
3. \$value_3: nome do certificado gerado. Ex.: "\*nome\*.pem".
4. \$value_4: número de dias de validade do certificado.
5. \$value_5: Código do país (duas letras). Ex.: BR.
6. \$value_6: Estado. Ex.: Alagoas.
7. \$value_7: Cidade. Ex.: Arapiraca.
8. \$value_8: Nome da Organização. Ex.:Universidade Estadual de Alagoas.
9. \$value_9: Departamento da Organização. Ex.: NUTEC.
10. \$value_10: Nome comum - Domínio. Ex.: portainer.uneal.
11. \$value_11: Endereço de Email. Ex.: nutec@gmail.com
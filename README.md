# Portainer Deploy

## Sumário

- [Portainer Deploy](#portainer-deploy)
  - [Sumário](#sumário)
  - [Sobre](#sobre)
  - [Instalação](#instalação)
  - [Geração de Certificado Digital (HTTPS)](#geração-de-certificado-digital-https)

## Sobre

O Portainer é um poderoso gerenciador de containers por trás de uma fácil interface de usuário (UI). Ele permite gerenciar e construir containers através de uma interface Web, além de permitir gerenciar containers que estão em outros dispositivos, Swarm e Kubernetes.

- Site: https://www.portainer.io/
- Docs: https://docs.portainer.io/

## Instalação
```yml
# docker-compose.yml

## Secção Volume
- /hostPath/docker.sock:/var/run/docker.sock # Volume_1
- /hostPath/data:/data # Volume_2
- /hostPath/letsencrypt/live/yourdomain:/certs/live/app:ro # Volume_3
- /hostPath/letsencrypt/archive/yourdomain:/certs/archive/app:ro # Volume_4
```
1. Volume_1: altere "/hostPath/docker.sock" pelo caminho do Docker em seu sistema operacional. No Linux, geralmente, "/var/run/docker.sock".

2. Volume_2: altere "/hostPath/data" para o local de salvamento de dados da aplicação.

3. Volume_3: altere "/hostPath/letsencrypt/live/yourdomain" para o local onde está armazenado os certificado digital ("\*nome\*.pem") e a chave privada ("\*nome\*.key"). [Instrução para geração de certificado.](#geração-de-certificado-digital-https)

4. Volume_4: altere "/hostPath/letsencrypt/archive/yourdomain" para o local de salvamento de dados relacionados ao certificado.

## Geração de Certificado Digital (HTTPS)

```bash
# Geração de chave privada RSA
$ openssl genpkey -out $value_1 -algorithm RSA -pkeyopt rsa_keygen_bits:$value_2

# Geração de certificado
$ openssl req -new -x509 -key $value_1 -out $value_3 -days $value_4 -subj="/C=$value_5/ST=$value_6/L=$value_7/O=$value_8/OU=$value_9/CN=$value_10/emailAddress=$value_11"
```
Valores (substitua):
- \$value_1: nome do arquivo para guardar a chave privada. Ex.: "\*nome\*.key".
- \$value_2: quantidade de bits da chave gerada. Ex.: 2048 (recomendado), 3072 (Ideal), 4096 (Forte - porém mais lento).
- \$value_3: nome do certificado gerado. Ex.: "\*nome\*.pem"
- \$value_4: número de dias de validade do certificado.
- \$value_5: Código do país (duas letras). Ex.: BR
- \$value_6: Estado. Ex.: Alagoas
- \$value_7: Cidade. Ex.: Arapiraca
- \$value_8: Nome da Organização. Ex.:Universidade Estadual de Alagoas
- \$value_9: Departamento da Organização. Ex.: NUTEC
- \$value_10: Nome comum - Domínio. Ex.: portainer.uneal
- \$value_11: Endereço de Email. Ex.: nutec@gmail.com
# Utilizando Vault com Secrets Operator

Este projeto tem como objetivo demonstrar a integração do Vault com o Secrets Operator para gerenciamento seguro de segredos em uma aplicação. O Vault é uma ferramenta de gerenciamento de segredos desenvolvida pela HashiCorp, enquanto o Secrets Operator é uma extensão para o Kubernetes que facilita a integração entre o Vault e as aplicações em execução no cluster.

## Configuração do Ambiente

### Subindo o ambiente utilizando o docker-compose

Antes de iniciar, certifique-se de ter o Docker instalado em sua máquina.

1. Clone o repositório do projeto:

```bash
git clone https://github.com/mouradev1/vault-secrets-operator-example.git
cd vault-secrets-operator-example/vault-docker
docker-compose up -d

```
## Configurando o vault-cli para ter acesso ao seu Vault
* Antes de prosseguir, instale o vault-cli em sua máquina. Execute os seguintes comandos:

```bash 

wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vault

```

## Configurando o vault-cli para ter acesso ao seu Vault

Antes de prosseguir, configure o vault-cli para acessar o seu Vault. Substitua `SEU-ENDEREÇO-DE-IP` pelo endereço IP correto.

```bash
export VAULT_ADDR="http://SEU-ENDEREÇO-DE-IP:8200"


```

## Inicializando o Vault
* Antes de utilizar o Vault, é necessário inicializá-lo. Execute o seguinte comando:

```bash

vault operator init

```

## Removendo o selo do Vault
* Após inicializar o Vault, você precisará remover o selo para desbloquear o acesso. Execute o seguinte comando:

```bash

vault operator unseal sua-chave

```


## Realizando login no Vault
* Após remover o selo, faça login no Vault:


```bash

vault login 

```


## Habilitando secrets no Vault
* Para habilitar o armazenamento de segredos no Vault, execute o seguinte comando:

```bash

vault secrets enable -path=dev kv

```

* Agora, vamos criar as credenciais dentro do nosso secrets:

```bash
vault kv put dev/pass user=admin pass=admin

```

* Em seguida, podemos aplicar nossa política (policy):

```bash
vault policy write policy-dev ./sources/policy-dev.hcl

```

* Para gerar um token para nossa política, execute o seguinte comando:

```bash
    vault token create -policy="policy-dev"
```

## Instalando o External Secrets Operator
* Para instalar o External Secrets Operator, execute os seguintes comandos:

```bash
helm repo add external-secrets https://charts.external-secrets.io
helm repo update
helm install external-secrets \
   external-secrets/external-secrets \
    -n external-secrets \
    --create-namespace \
    --set installCRDs=true
```

* Em seguida, crie a secret para armazenar o token criado:

```bash

kubectl create secret generic vault-policy-token --from-literal=token=seutoken

```

# Por fim, crie o ClusterSecretStore e o External Secrets:

```bash 
kubectl apply -f sources/vault-secrets.yaml 

```
## Documentações de Referências

Aqui estão algumas documentações de referência que podem ser úteis para entender melhor o Vault, o Secrets Operator e o Helm:

- [Documentação do Vault](https://developer.hashicorp.com/vault)
- [Documentação do Secrets Operator](https://external-secrets.io/v0.8.5)
- [Documentação do Helm](https://helm.sh/docs)
- [Documentação do Kind](https://kind.sigs.k8s.io/docs/user/quick-start)
* Essas documentações fornecem informações detalhadas sobre os recursos, funcionalidades e exemplos de uso dessas ferramentas. Recomenda-se consultá-las para obter mais informações e aprofundar-se nos tópicos abordados neste projeto.

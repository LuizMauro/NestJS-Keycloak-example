## Overview

A API contem 4 endpoints basicos para autenticação:

- `POST /auth/login`
- `GET /auth/me`
- `POST /auth/refresh`
- `POST /auth/logout`

## Rodando!

Já existe um `docker-compose.yaml` configurado neste projeto. Porém, é importante seguir o tutorial abaixo para obter as variáveis de ambiente e configurações corretas para o projeto funcionar.

### Keycloak

Vamos terminar de configurar o Keycloak. Graças ao arquivo [nestjs-keycloak-realm.json](./nestjs-keycloak-realm.json) não precisaremos perder tanto tempo nesta configuração.

No terminal, na raiz do projeto, execute o comando para iniciar o Keycloak:

```sh
docker-compose up -d keycloak
```

Vamos verificar nos logs se o Keycloak foi iniciado:

```sh
docker-compose logs -f keycloak | grep -i "Admin console listening on"
```

#### Criando o arquivo de variáveis de ambiente

Copie o arquivo de variáveis de ambiente de exemplo deste repositório para um arquivo `.env` na raiz do projeto:

```sh
cp .env.example .env
```

> Este arquivo é necessário para executarmos a API do `docker-compose.yaml`.

**Vamos substituir o que está em `<REPLACE_ME>` pelas informações que obtivemos do nosso Keycloak.**

#### Concluindo as configurações do Keycloak

Acesse o endereço: http://localhost:8080. Você verá a tela inicial do Keycloak.

Acesse **Console de administração** e faça login com as credenciais:

- Nome de usuário ou e-mail: admin
- Senha: admin

Você acessará diretamente o Realm que foi configurado com a ajuda de [nestjs-keycloak-realm.json](./nestjs-keycloak-realm.json).

Agora precisamos:

1. Obtenha a chave RSA pública do Realm;
1. Redefinir o segredo do cliente `nestjs-keycloak`;
1. Crie um usuário de teste para nosso Realm.

#### Obtendo a chave publica RSA do Realm

Acesse o link: http://localhost:8080/auth/admin/master/console/#/realms/nestjs-keycloak/keys.

Na chave RSA com o provedor `rsa-generated`, clique em **Public Key**.

Copie o conteúdo exibido e cole-o no arquivo `.env` que criamos anteriormente, substituindo o valor `<REPLACE_ME>` pela chave obtida da variável `KEYCLOAK_REALM_RSA_PUBLIC_KEY`. Será parecido com:

```diff
- KEYCLOAK_REALM_RSA_PUBLIC_KEY="-----BEGIN PUBLIC KEY-----\n<REPLACE_ME>\n-----END PUBLIC KEY-----"
+ KEYCLOAK_REALM_RSA_PUBLIC_KEY="-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAlOSbkRWDCFLl0dslyU1aYkACfos+wib22LHWTz9cgd+RBByS43wmxKNe90b5g6S0RMJvBUpcDnnUNMLXgP7EyfUluWriiUpyWXBLclhtWHz49QZYAOuR4T+4C2pmCfAkefDz5tbN+SQuRyZcJzZ/cLboKfKzwK7Nlud6NRvYMypOxmSNaDuQAKWH8BciT6ahpSFFgPWMBuGD5oz9DYqZKNNaXedmVLqL911EC0kH0J54AOjM4OKuo+sTUji6eJFCJDMynnJIJyTLFDRrhLM1aD+n77q0k59Bm/EwtP77tDT5uR0AWfhErdsZQV863TjBDcTEsxveEtOJmMF9L/6a8QIDAQAB\n-----END PUBLIC KEY-----"
```

#### Obtendo o Client Secret

Na barra esquerda, vá para `Clients / nestjs-keycloak / Credentials` e cliquem em **Regenerate Secret**.

Copie o conteúdo exibido e cole-o no arquivo `.env` que criamos anteriormente, substituindo o valor `<REPLACE_ME>` pelo segredo obtido da variável `KEYCLOAK_CLIENT_SECRET`. Será parecido com:

```diff
- KEYCLOAK_CLIENT_SECRET="<REPLACE_ME>"
+ KEYCLOAK_CLIENT_SECRET="2387cbc8-7bef-4f09-bd05-7f97ca4ae813"
```

#### Criando o usuário de teste

Acesse o link: http://localhost:8080/auth/admin/master/console/#/create/user/nestjs-keycloak

1. Insira as informações:

   - Username: test
   - Email: test@test.com

  > **Deixe as outras opções como estão.**

2. Clique em `Save`.

3. Ainda na tela do usuário recém-criado, clique em `Credenciais` e insira os dados:
   - Password: test
   - Password Confirmation: test
   - Temporary: OFF (deixe em OFF)

Clique em **Set Password** e confirme a operação.

### NestJS

Vamos rodar nossa API Nest. Execute o comando no terminal:

```sh
docker-compose up -d app
```

Aguarde a compilação e execução do aplicativo.

Veja os logs com:

```sh
docker-compose logs -f app
```
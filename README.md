# Dev Machine
Configuração da máquina de desenvolvimento.

# Implantação
Crie um OAuth App no GitHub:
```steps
Settings -> Developer settings -> OAuth Apps -> New OAuth App
```

> Use o comando `openssl rand -hex 32` para gerar os segredos.

Copie e configure as ENVs:
```bash
cp example.env .env
```

Crie as redes:
```bash
docker network create postgres_net

docker network create traefik_net
```

Crie o arquivo acme.json.

```bash
touch acme.json && chmod 600 acme.json
```

Inicie o container do Postgres:
```bash
docker compose up postgres -d
```

Crie os bancos de dados:
- keycloak
- woodpecker

Inicie os outros containers:
```bash
docker compose up -d
```

Para implantar uma aplicação nova que não seja da infra crie um arquivo `.yaml` no diretório `dynamic` com o nome da aplicação. Por exemplo:
```yaml
# dynamic/example.yaml
http:
  routers:
    example:
      rule: PathPrefix(`/example/api/v1`)
      service: example
      middlewares:
        - strip-prefix

  middlewares:
    strip-prefix:
      stripPrefix:
        prefixes:
          - /example/api/v1

  services:
    example:
      loadBalancer:
        servers:
          - url: "http://example:8080"
```

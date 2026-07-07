# unomi-test

Este repositório sobe um ambiente local do **Apache Unomi 3** com **Elasticsearch** via Docker Compose.

## Acesso rapido

- Console/cluster Unomi: `https://localhost:9443/cxs/cluster`
- Console SSH do Karaf: `ssh -p 8102 karaf@localhost`
- Unomi Studio: `http://localhost:8085`
- Credenciais padrao (UI e SSH): `karaf` / `karaf`

## 1) O que e o Apache Unomi

O **Apache Unomi** e uma plataforma open source de **Customer Data Platform (CDP)** e **personalizacao em tempo real**.

Com ele, voce consegue:

- coletar eventos de navegacao e interacoes dos usuarios;
- manter perfis e sessoes unificados;
- criar regras para enriquecer perfis automaticamente;
- segmentar usuarios e aplicar personalizacao de conteudo.

O endpoint principal para contexto de visitante e o `/cxs/context.json`, e o envio de eventos publicos costuma ser feito por `/cxs/eventcollector`.

## 2) Como subir o ambiente e rodar o projeto

As instrucoes abaixo seguem o tutorial oficial: https://unomi.apache.org/tutorial.html

### Pre-requisitos

- Docker instalado e em execucao;
- Docker Compose v2 (`docker compose`).

### Estrutura deste projeto

Este repositorio ja possui o arquivo `docker-compose.yml` com:

- `elasticsearch` em `docker.elastic.co/elasticsearch/elasticsearch:9.1.3`;
- `unomi` em `apache/unomi:3.0.0`;
- `unomi-studio` em `grootan/unomi-studio:1.0.1`;
- portas expostas:
  - `9200` (Elasticsearch)
  - `8181` (HTTP API Unomi)
  - `9443` (HTTPS API Unomi)
  - `8102` (SSH do Karaf)
  - `8085` (Unomi Studio HTTP)
  - `8445` (Unomi Studio HTTPS)

### Subindo o ambiente

Na raiz do projeto, execute:

```bash
docker compose up
```

Se preferir rodar em background:

```bash
docker compose up -d
```

Aguarde de 1 a 2 minutos para Elasticsearch e Unomi inicializarem completamente.

### Validando se esta funcionando

1. Verifique o cluster do Unomi no navegador:

```text
https://localhost:9443/cxs/cluster
```

Credenciais padrao: `karaf` / `karaf`.

Observacao: aceite o aviso de certificado autoassinado (esperado em ambiente de desenvolvimento).

2. Verifique a saude do Elasticsearch:

```bash
curl http://localhost:9200/_cat/health?format=json
```

3. Teste um contexto basico do Unomi:

```text
http://localhost:8181/cxs/context.json?sessionId=1234
```

4. Verifique o Unomi Studio:

```text
http://localhost:8085
```

Observacao: o Unomi Studio e um projeto comunitario (nao oficial da Apache Foundation) e pode nao cobrir 100% dos recursos do Unomi 3.

### Primeira chamada de API (exemplo)

```bash
curl -X POST "http://localhost:8181/cxs/context.json?sessionId=1234" \
  -H "Content-Type: application/json" \
  --data-raw '{
    "source": {
      "itemId": "homepage",
      "itemType": "page",
      "scope": "example"
    },
    "requiredProfileProperties": ["*"],
    "requiredSessionProperties": ["*"],
    "requireSegments": true,
    "requireScores": true
  }'
```

### Console Karaf (opcional)

Para acessar o console SSH do Karaf:

```bash
ssh -p 8102 karaf@localhost
```

Credenciais do console Karaf: `karaf` / `karaf`.

Comandos uteis no Karaf: `event-tail`, `profile-list`, `rule-list`.

### Parando o ambiente

```bash
docker compose down
```

Para remover tambem os volumes:

```bash
docker compose down -v
```

## Referencias

- Tutorial oficial: https://unomi.apache.org/tutorial.html
- Documentacao completa: https://unomi.apache.org/manual/latest/index.html
- REST API: https://unomi.apache.org/rest-api-doc/index.html

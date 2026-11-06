# Instalação limpa — Fitoway AI Platform

Este procedimento instala **uma nova instância** do Open WebUI. Não importa conversas, usuários, chaves ou dados da VPS Fitoway. O código da interface é fornecido pela imagem oficial do Open WebUI, sem copiar o código-fonte upstream.

## Pré-requisitos

- Docker Engine e Docker Compose v2 instalados e funcionando.
- Git.
- `openssl` para gerar uma chave persistente.
- Conexão à internet para baixar imagens. Para usar modelos locais, também será necessário baixar os modelos.
- Recursos de CPU, RAM e armazenamento suficientes para o modelo escolhido.

## Instalar

```bash
git clone https://github.com/alemaodacapa/fitoway-ai-platform.git
cd fitoway-ai-platform
cp .env.example .env
```

Edite `.env` e **substitua** `CHANGE_ME_GENERATE_WITH_OPENSSL` por uma chave aleatória. Em Linux/macOS, gere com:

```bash
openssl rand -hex 32
```

Em Windows, gere uma chave aleatória de maneira segura com ferramenta apropriada, ou execute o comando em WSL. Não publique o valor.

Para uso no computador local, altere `WEBUI_BIND_ADDRESS=127.0.0.1` apenas se desejar acesso remoto. Se publicar na internet, use um proxy HTTPS e autenticação adequada.

Inicie somente a interface:

```bash
docker compose up -d
```

Acesse [http://localhost:3000](http://localhost:3000). A primeira conta criada poderá assumir o papel administrativo, conforme a versão e configuração do Open WebUI. Crie sua conta antes de expor a instância publicamente.

### Com Ollama local (CPU)

```bash
docker compose --profile local up -d
docker compose exec ollama ollama pull llama3.2
```

A imagem `ollama/ollama` deste exemplo usa CPU por padrão. O download e a execução do modelo exigem memória e armazenamento suficientes. Para GPU, consulte a documentação oficial do Ollama e configure o Docker/NVIDIA Container Toolkit.

### Usando APIs externas

Inicie apenas o Open WebUI e configure provedores em **Admin → Configurações → Conexões**. Chaves do Gemini, OpenRouter e outros provedores são **individuais**, não são fornecidas neste repositório.

## Operação

```bash
docker compose ps
docker compose logs --tail 100 open-webui
docker compose down
```

`docker compose down` interrompe os serviços e preserva os volumes nomeados. **Não execute `docker compose down -v` se quiser manter conversas e modelos.**

Para atualizar, faça backup antes e então:

```bash
docker compose pull
docker compose up -d
```

Para ambientes de produção, fixe `OPEN_WEBUI_TAG` em uma versão estável específica, em vez de `main`.

## Isolamento

O Compose utiliza o projeto `fitoway-ai-platform`, os volumes próprios `webui_data` e `ollama_data`, e uma rede própria. **Não utiliza** `/docker/open-webui`, `postgresql-mvlp_default`, `open-webui_open_webui_data` ou configurações privadas de `fitoway.tech`.

> **Atenção:** não execute esta instalação de demonstração na VPS de produção Fitoway sem revisar nomes de projetos, portas, volumes e redes. O arquivo é destinado a instalações novas.

## Limitações atuais

- Traefik e HTTPS são opcionais e precisam de configuração própria por ambiente.
- Open Terminal não é ativado por padrão por motivos de segurança.
- PostgreSQL não é necessário para a instalação limpa padrão.
- Não inclui banco de dados ou usuários pré-criados.
- O projeto ainda não possui teste de ponta a ponta executado em uma instalação limpa; o Compose foi preparado com base nas recomendações oficiais.

Referências: [Open WebUI Quick Start](https://docs.openwebui.com/getting-started/quick-start/) e [Open WebUI Updating](https://docs.openwebui.com/getting-started/updating/).

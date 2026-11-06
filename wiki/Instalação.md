# 🚀 Instalação limpa

Esta página descreve uma instalação **nova**, sem importar dados da implantação hospedada em fitoway.tech.

## 1. Pré-requisitos

- Git e conexão à internet.
- Docker Engine funcional e Docker Compose v2.
- `openssl` ou ferramenta equivalente para gerar uma chave secreta.
- Capacidade de RAM, CPU e disco suficiente para os modelos escolhidos.

## 2. Clonar o projeto

```bash
git clone https://github.com/alemaodacapa/fitoway-ai-platform.git
cd fitoway-ai-platform
cp .env.example .env
```

## 3. Preparar o segredo

Gere um valor aleatório:

```bash
openssl rand -hex 32
```

Edite `.env` e substitua `CHANGE_ME_GENERATE_WITH_OPENSSL` pelo valor gerado. **Não envie o arquivo `.env` ao GitHub.**

## 4. Iniciar o Open WebUI

```bash
docker compose up -d
docker compose ps
```

Acesse **http://localhost:3000** no mesmo computador. Crie sua conta inicial seguindo as orientações exibidas pelo Open WebUI.

## 5. Ativar Ollama opcional

```bash
docker compose --profile local up -d
docker compose exec ollama ollama pull llama3.2
```

O uso de modelos locais pode exigir mais RAM e armazenamento.

## 6. Operar e atualizar

```bash
docker compose logs --tail 100 open-webui
docker compose pull
docker compose up -d
```

Antes de atualizar, faça backup dos volumes. **Nunca use `docker compose down -v` se quiser preservar os dados.**

## Instalação pública

O Compose publica `127.0.0.1:3000` por padrão, ou seja, acesso local. Para hospedar publicamente, configure HTTPS, autenticação, política de cadastro e proxy reverso. Não copie automaticamente os caminhos, redes ou volumes da VPS Fitoway.

📖 [Guia completo da instalação limpa](https://github.com/alemaodacapa/fitoway-ai-platform/blob/main/docs/clean-install.md)

⬅️ [[Home]] · ⚙️ [[Configuração]]

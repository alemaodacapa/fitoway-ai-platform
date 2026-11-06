# 🔧 Solução de problemas

Use os comandos abaixo no diretório do projeto instalado.

## O Docker Compose não inicia

```bash
docker compose version
docker compose config --quiet
docker compose ps
```

Se aparecer erro de `WEBUI_SECRET_KEY`, crie o arquivo `.env` baseado no `.env.example` e substitua o valor de exemplo por uma chave gerada com segurança.

## Não consigo abrir a interface

A configuração padrão publica o endereço **http://localhost:3000** apenas no computador que executa o Docker. Para acesso remoto, é necessário configurar a rede e um proxy HTTPS.

```bash
docker compose ps
docker compose logs --tail 100 open-webui
```

## O Ollama não aparece

O Ollama só é iniciado quando o perfil opcional `local` está ativo:

```bash
docker compose --profile local up -d
docker compose exec ollama ollama list
```

Se não houver modelos instalados:

```bash
docker compose exec ollama ollama pull llama3.2
```

## Erro com provedor externo

Verifique a chave de API, o endpoint, a compatibilidade do modelo e se o provedor suporta a funcionalidade solicitada. Um endpoint de Chat Completions pode não oferecer a API Responses ou chamadas de ferramentas.

## Evite perda de dados

Não use `docker compose down -v` para reiniciar serviços: `-v` remove volumes nomeados do projeto e pode apagar conversas e modelos.

## Reportar um problema

Abra uma [Issue](https://github.com/alemaodacapa/fitoway-ai-platform/issues) com os passos para reproduzir, versões e mensagens de erro, **removendo tokens, senhas, dados pessoais e informações privadas**.

⬅️ [[Home]] · 🚀 [[Instalação]]

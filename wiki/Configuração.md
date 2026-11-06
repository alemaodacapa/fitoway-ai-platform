# ⚙️ Configuração do Fitoway AI Platform

## Variáveis de ambiente

Copie `.env.example` para `.env` e ajuste os valores conforme a instalação. Não publique o arquivo real.

| Variável | Finalidade |
|---|---|
| `WEBUI_SECRET_KEY` | Chave secreta obrigatória; gere um valor aleatório |
| `WEBUI_BIND_ADDRESS` | Endereço de escuta; padrão `127.0.0.1` |
| `WEBUI_PORT` | Porta local; padrão `3000` |
| `WEBUI_NAME` | Nome mostrado na interface |
| `ENABLE_SIGNUP` | Habilita/desabilita novos cadastros |
| `OPEN_WEBUI_TAG` | Tag da imagem do Open WebUI |
| `OLLAMA_TAG` | Tag da imagem Ollama |

Após criar a conta administrativa, considere mudar `ENABLE_SIGNUP=false` e aplicar a configuração novamente com `docker compose up -d`.

## Modelos locais com Ollama

```bash
docker compose --profile local up -d
docker compose exec ollama ollama pull llama3.2
```

O Compose configura o endereço interno do Ollama como `http://ollama:11434`, mas o serviço Ollama só inicia com o perfil `local`. Se quiser usar exclusivamente APIs externas, você pode iniciar somente a interface.

## Gemini e OpenRouter

O Open WebUI oferece conexões com provedores de modelos, dependendo da versão instalada. Cadastre seus próprios endpoints e credenciais nas configurações administrativas. A integração exata varia conforme o provedor, o formato de API e o suporte a ferramentas.

**Atenção:** nem todos os endpoints compatíveis com OpenAI suportam todos os recursos, como a API Responses e chamadas de ferramentas.

## Publicação na internet

O arquivo `compose.yaml` é voltado a uma instalação local segura por padrão. Não altere `WEBUI_BIND_ADDRESS` para expor publicamente o serviço sem configurar proxy HTTPS, controle de acesso e backups.

⬅️ [[Home]] · 🔧 [[Solução de problemas]]

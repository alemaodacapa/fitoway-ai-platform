# 🤖 Fitoway AI Platform — Wiki Oficial

Bem-vindo à documentação em português do **Fitoway AI Platform**! 🚀

O Fitoway AI Platform é uma distribuição independente, baseada no **Open WebUI**, para criar uma interface de inteligência artificial auto-hospedada com Docker. Você pode utilizar modelos locais com **Ollama** ou configurar provedores externos, como **Gemini** e **OpenRouter**, com suas próprias credenciais.

> [!IMPORTANT]
> Este projeto é independente do Open WebUI oficial. A configuração atual do repositório é voltada para uma **instalação limpa via Docker**, e **ainda não foi validada em um teste de ponta a ponta**. A implantação de produção em [fitoway.tech](https://fitoway.tech) possui configurações próprias que não são distribuídas neste repositório.

## 📚 Navegue pela documentação

- 🚀 **[[Instalação]]** — pré-requisitos, instalação Docker e Ollama opcional.
- ⚙️ **[[Configuração]]** — variáveis de ambiente, modelos externos e acesso à plataforma.
- 🔧 **[[Solução de problemas]]** — comandos de diagnóstico e erros frequentes.
- 🤝 **[[Como contribuir]]** — sugestões, Pull Requests e boas práticas.

## ⚡ Início rápido

Você precisará de **Git**, **Docker Engine** e **Docker Compose v2**.

```bash
git clone https://github.com/alemaodacapa/fitoway-ai-platform.git
cd fitoway-ai-platform
cp .env.example .env
```

Gere uma chave secreta com `openssl rand -hex 32` e substitua o valor `CHANGE_ME_GENERATE_WITH_OPENSSL` no arquivo `.env`. Em seguida:

```bash
docker compose up -d
```

Abra **http://localhost:3000** no computador que executa o Docker.

Para iniciar também o Ollama:

```bash
docker compose --profile local up -d
docker compose exec ollama ollama pull llama3.2
```

> A configuração padrão escuta somente em `127.0.0.1`; para acesso externo, prepare HTTPS, autenticação e proxy reverso antes de expor o serviço.

## 🧩 Componentes

| Componente | Papel | Na instalação limpa |
|---|---|---|
| Open WebUI | Interface de conversação e administração | Incluído |
| Docker Compose | Inicialização, redes e volumes | Incluído |
| Ollama | Execução de modelos locais | Opcional (perfil `local`) |
| Gemini / OpenRouter | Provedores de IA externos | Configuração manual |
| Traefik | Proxy HTTPS | Não incluído na instalação padrão |
| Open Terminal | Integração com ferramentas de terminal | Não habilitado por padrão |

## 🔐 Privacidade e segurança

Este repositório **não distribui** contas, conversas, dados do servidor, senhas, chaves de API ou arquivos `.env` reais. Cada instalação cria seus próprios dados persistentes. Evite publicar serviços diretamente na internet antes de proteger acesso, TLS e cadastro de usuários.

## 🌎 Comunidade

- 💻 [Código e arquivos do projeto](https://github.com/alemaodacapa/fitoway-ai-platform)
- 💬 [Discussions](https://github.com/alemaodacapa/fitoway-ai-platform/discussions)
- 🐛 [Issues](https://github.com/alemaodacapa/fitoway-ai-platform/issues)
- ✅ [GitHub Actions](https://github.com/alemaodacapa/fitoway-ai-platform/actions)
- 📖 [Guia detalhado no repositório](https://github.com/alemaodacapa/fitoway-ai-platform/blob/main/docs/clean-install.md)
- 🧠 [Open WebUI — projeto de origem](https://github.com/open-webui/open-webui)

⭐ Se esta distribuição for útil, deixe uma estrela no repositório!

---

*Documentação mantida pela comunidade Fitoway AI Platform. O projeto é independente e deve respeitar as licenças e atribuições dos componentes utilizados.*

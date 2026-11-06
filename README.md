<div align="center">

# 🤖 Fitoway AI Platform
### Plataforma de inteligência artificial self-hosted

**Open WebUI • Ollama • Gemini • OpenRouter • Open Terminal • Docker • Traefik**

[![Docker](https://img.shields.io/badge/Docker-Container-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/)
[![Open WebUI](https://img.shields.io/badge/Powered%20by-Open%20WebUI-2563eb)](https://docs.openwebui.com/)
[![Ubuntu](https://img.shields.io/badge/Server-Ubuntu%2024.04-E95420?logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![GitHub last commit](https://img.shields.io/github/last-commit/alemaodacapa/fitoway-ai-platform)](https://github.com/alemaodacapa/fitoway-ai-platform/commits/main)

**[🌐 Acessar fitoway.tech](https://fitoway.tech)** · **[📖 Documentação Open WebUI](https://docs.openwebui.com/)** · **[🔧 Repositório original](https://github.com/open-webui/open-webui)**

[![Demonstração do Open WebUI](https://cdn.openwebui.com/open-webui-demo.gif)](https://docs.openwebui.com/)

</div>

> [!IMPORTANT]
> **Projeto independente de implantação e integração**, baseado no Open WebUI. Este repositório **não é o código-fonte oficial** nem contém, por enquanto, uma cópia auditada da configuração da VPS. Não publique senhas, tokens, bancos de dados, conversas ou volumes Docker.

## 🚀 Instalação limpa para qualquer pessoa

Este repositório agora contém um **Compose independente** na raiz, sem dependências da VPS Fitoway. O Docker baixa a imagem oficial do Open WebUI, cria uma rede própria e mantém os dados em volumes locais. A instalação não contém usuários, conversas, chaves ou configurações privadas.

```bash
git clone https://github.com/alemaodacapa/fitoway-ai-platform.git
cd fitoway-ai-platform
cp .env.example .env
# Gere uma chave com: openssl rand -hex 32
# Edite .env e substitua CHANGE_ME_GENERATE_WITH_OPENSSL pela chave gerada.
docker compose up -d
```

Abra **http://localhost:3000** no computador que executa Docker. Para incluir Ollama e baixar um modelo:

```bash
docker compose --profile local up -d
docker compose exec ollama ollama pull llama3.2
```

**Importante:** a configuração padrão só publica a interface em `127.0.0.1`. Para acesso remoto, configure HTTPS e autenticação antes de alterar esse endereço. Após criar o administrador, considere definir `ENABLE_SIGNUP=false` em `.env` e recriar o serviço.

📘 **[Guia detalhado de instalação limpa](docs/clean-install.md)** · **[Compose independente](compose.yaml)** · **[Modelo de variáveis](.env.example)**

> Este repositório fornece uma **distribuição via Docker**, não uma cópia integral do código-fonte do Open WebUI. O código upstream está disponível no [projeto oficial](https://github.com/open-webui/open-webui). O procedimento ainda não foi validado em teste ponta a ponta.

## 📑 Índice

- [Sobre](#-sobre)
- [Recursos e arquitetura](#-recursos-e-arquitetura)
- [Ambiente Fitoway](#-ambiente-fitoway)
- [Requisitos](#-requisitos)
- [Instalação com Docker](#-instalação-com-docker)
- [Instalação com Python](#-instalação-com-python)
- [Traefik e HTTPS](#-traefik-e-https)
- [Configuração de modelos e provedores](#-configuração-de-modelos-e-provedores)
- [Open Terminal](#-open-terminal)
- [Backup, atualização e segurança](#-backup-atualização-e-segurança)
- [Solução de problemas](#-solução-de-problemas)
- [Estrutura e roadmap](#-estrutura-e-roadmap)
- [Créditos e licenças](#-créditos-e-licenças)

## 💡 Sobre

A **Fitoway AI Platform** reúne modelos de IA locais e provedores em nuvem em uma interface central. Sua base é o [Open WebUI](https://github.com/open-webui/open-webui), plataforma extensível e self-hosted para conversas, gerenciamento de modelos, ferramentas e recursos de conhecimento.

Com **Ollama**, a plataforma pode executar modelos localmente; com **Gemini**, **OpenRouter** ou outras APIs compatíveis com OpenAI, pode consultar modelos externos. O funcionamento **100% offline** só é possível quando todos os modelos e serviços necessários estão disponíveis localmente.

**Objetivos do projeto:**
- Centralizar a experiência de IA em `https://fitoway.tech`.
- Oferecer acesso a modelos locais e remotos.
- Integrar ferramentas e terminais com controle de permissões.
- Manter implantação reproduzível, monitorável e segura.
- Documentar configurações e procedimentos de manutenção.

## 🧩 Recursos e arquitetura

| Componente | Finalidade | Estado conhecido |
|---|---|---|
| Open WebUI | Interface web, usuários, chats e modelos | Em execução na VPS |
| Ollama | Modelos de linguagem locais | Serviço/endereço observado; modelos específicos não inventariados |
| Docker | Contêineres e persistência | Em uso |
| Traefik | Proxy reverso e HTTPS | Em execução |
| Google Gemini | Modelos externos via API | API Google habilitada; integração ainda em validação |
| OpenRouter | Roteamento entre provedores | Erro de tool calling identificado; validação pendente |
| Open Terminal | Terminal e navegação de arquivos | Contêiner presente; conexão no painel pendente de validação |
| PostgreSQL | Serviço de banco relacional | Contêiner presente; vínculo com Open WebUI não confirmado |
| Knowledge / Tools | Conhecimento e ferramentas | Recursos dependentes da versão e configuração |

```text
                       Navegador
                          |
                  https://fitoway.tech
                          |
                    Traefik / TLS
                          |
                       Open WebUI
                   _______|________
                  /       |        \
               Ollama   APIs       Ferramentas
               local    externas   Open Terminal
                      Gemini /
                      OpenRouter
```

A comunicação entre serviços deve preferir redes internas Docker e autenticação apropriada. Não exponha portas administrativas sem necessidade.

## 🖥️ Ambiente Fitoway

**Infraestrutura observada em novembro de 2026:**
- VPS Hostinger com Ubuntu 24.04 LTS.
- Contêineres identificados: `open-webui`, `traefik-traefik-1`, `open-terminal` e PostgreSQL.
- Open WebUI respondendo HTTP **200** em `http://127.0.0.1:8080/` na VPS.
- Domínio `https://fitoway.tech/` respondendo HTTP **200** a partir da VPS.
- UFW observado como **inativo** na última verificação.

> Esses resultados são históricos e não garantem a disponibilidade atual. A configuração de produção não deve ser sobrescrita por exemplos genéricos deste documento.

### Comandos de inspeção (somente leitura)

Execute **na VPS Ubuntu**, não no PowerShell:

```bash
docker ps
docker inspect open-webui --format '{{.Config.Image}}'
docker inspect open-webui --format '{{range .Mounts}}{{.Type}} | {{.Destination}}{{println}}{{end}}'
docker logs --tail 40 open-webui
docker logs --tail 40 traefik-traefik-1
curl -k -sS -o /dev/null -w 'HTTPS: %{http_code}\n' https://fitoway.tech/
curl -sS -o /dev/null -w 'Open WebUI: %{http_code}\n' http://127.0.0.1:8080/
```

## 📋 Requisitos

**Docker:** Docker Engine, Docker Compose (para a opção Compose), volume persistente, portas disponíveis e recursos adequados ao modelo utilizado. Modelos locais podem exigir RAM, armazenamento e GPU consideráveis.

**Python:** Python **3.12** e pip, conforme a referência de instalação do Open WebUI utilizada neste projeto.

**Produção:** domínio, DNS, TLS, backups, autenticação administrativa e plano de recuperação.

## 🐳 Instalação com Docker

### Instalação nova, isolada, sem Traefik

```bash
docker volume create open-webui-data

docker run -d \
  --name open-webui \
  --restart unless-stopped \
  -p 127.0.0.1:3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui-data:/app/backend/data \
  ghcr.io/open-webui/open-webui:main
```

Acesse `http://localhost:3000` no servidor, ou use túnel SSH para acesso remoto. A porta está vinculada ao loopback, não à internet pública.

> [!CAUTION]
> **Não execute esse comando na VPS Fitoway atual**: o contêiner `open-webui` já existe. Este é um exemplo para instalações novas, não um procedimento de migração.

### Exemplo com Docker Compose

Crie `compose.yaml` **em um diretório separado de qualquer implantação existente**:

```yaml
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    restart: unless-stopped
    ports:
      - "127.0.0.1:3000:8080"
    extra_hosts:
      - "host.docker.internal:host-gateway"
    volumes:
      - open-webui-data:/app/backend/data

volumes:
  open-webui-data:
```

```bash
docker compose up -d
docker compose ps
```

### Imagens disponíveis no ecossistema

| Tag | Descrição |
|---|---|
| `:main` | Imagem padrão |
| `:slim` | Variante reduzida; certas dependências podem ser externas |
| `:cuda` | Variante com CUDA |
| `:ollama` | Variante com Ollama integrado |
| `:dev` | Desenvolvimento; não recomendada para produção |

Confirme as tags e requisitos na [documentação oficial](https://docs.openwebui.com/getting-started/quick-start/). Para produção, prefira versões fixas e atualizações controladas.

## 🐍 Instalação com Python

```bash
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install open-webui
open-webui serve
```

Abra `http://localhost:8080`. Consulte a [documentação de instalação](https://docs.openwebui.com/getting-started/) para diferenças entre sistemas operacionais.

**Aplicativo desktop:** [Open WebUI Desktop](https://github.com/open-webui/desktop).

## 🔐 Traefik e HTTPS

Na instalação Fitoway, o Traefik encaminha o tráfego HTTPS para o Open WebUI. Para reproduzir essa arquitetura, configure:

1. DNS apontando para a VPS.
2. Entradas HTTP/HTTPS e certificado TLS.
3. Rede Docker compartilhada ou roteamento válido entre proxy e aplicação.
4. Roteador com host `fitoway.tech`.
5. Serviço apontando para a porta interna correta do Open WebUI.
6. Autenticação e cabeçalhos de segurança.

**Não publique um arquivo Traefik de produção antes de conferir labels, redes, volumes, certificados e variáveis reais.** Consulte a [documentação Traefik](https://doc.traefik.io/traefik/).

## 🧠 Configuração de modelos e provedores

No Open WebUI, procure **Admin → Configurações → Conexões** (nomes podem variar por versão).

### Ollama — modelos locais

Instale e configure o [Ollama](https://ollama.com/download). Em uma instalação Ollama independente:

```bash
ollama list
ollama pull llama3.2
```

No Open WebUI, aponte para a URL Ollama acessível **a partir do contêiner**. Se o Ollama estiver em outro contêiner, use o nome DNS interno na rede Docker. `localhost` dentro de um contêiner não é automaticamente o host.

### Google Gemini

1. Crie uma chave no [Google AI Studio](https://aistudio.google.com/api-keys).
2. Adicione uma conexão compatível com OpenAI, se suportada pela versão.
3. Utilize a URL base:

```text
https://generativelanguage.googleapis.com/v1beta/openai/
```

4. Informe a chave **somente** no painel administrativo seguro.
5. Teste com um modelo disponível, por exemplo `gemini-2.5-flash`.

**Erro conhecido:** a tentativa de usar `/v1beta/openai/responses` retornou **HTTP 404**. Nesse caso, verifique a compatibilidade da *Responses API* e teste **Chat Completions**, ou a integração Gemini nativa se disponível. Consulte [Gemini OpenAI compatibility](https://ai.google.dev/gemini-api/docs/openai).

O `gcloud` utilizado no **Google Cloud Shell** não precisa ser instalado na VPS para uma conexão baseada em chave de API.

### OpenRouter

URL base típica:

```text
https://openrouter.ai/api/v1
```

Use a chave privada da sua conta [OpenRouter](https://openrouter.ai/docs).

**Erro observado:**

```text
No endpoints found that support tool use.
Try disabling "get_current_timestamp".
```

A requisição solicitou *tool calling*, mas nenhum endpoint disponível na rota selecionada aceitou essa capacidade. Para testar:

1. Abra uma conversa nova sem ferramentas.
2. Desative temporariamente `get_current_timestamp` e outras ferramentas associadas ao modelo.
3. Verifique as capacidades e ferramentas em **Workspace → Modelos**.
4. Escolha um modelo/provedor com suporte confirmado a *tool calling*, se necessário.
5. Revise as opções de [provider routing](https://openrouter.ai/docs/guides/routing/provider-selection).

A tela de **servidores de ferramentas externas** estar vazia não significa que as ferramentas integradas estejam desativadas.

## 🖥️ Open Terminal

O [Open Terminal](https://github.com/open-webui/open-terminal) adiciona recursos de terminal e arquivos ao ecossistema. O contêiner `open-terminal` já foi observado na VPS; a integração com o painel ainda precisa ser confirmada.

Para verificar redes e portas, **sem alterar contêineres**:

```bash
docker inspect --format '{{.Name}} | Redes: {{range $n, $v := .NetworkSettings.Networks}}{{$n}} {{end}} | Portas: {{json .NetworkSettings.Ports}}' open-webui open-terminal
```

No Open WebUI, procure **Admin → Integrações → Open Terminal**, configure o endereço interno, autenticação e permissões conforme a versão instalada.

> [!WARNING]
> Não disponibilize root, socket Docker, chaves SSH ou acesso irrestrito à VPS para ferramentas de IA. Utilize isolamento, contas sem privilégios e confirmação humana para ações destrutivas.

## 💾 Backup, atualização e segurança

### Persistência e backup

O diretório persistente padrão do Open WebUI em Docker costuma ser `/app/backend/data`. Confira o volume **real**:

```bash
docker inspect open-webui --format '{{range .Mounts}}{{.Type}} | {{.Source}} -> {{.Destination}}{{println}}{{end}}'
```

Faça backups consistentes dos volumes e bancos efetivamente utilizados, guarde cópias **criptografadas fora da VPS** e teste restaurações em ambiente isolado. **Nunca** envie dumps, conversas ou dados de usuários ao GitHub.

### Atualizações

1. Registre as versões e faça backup testado.
2. Leia as notas de versão.
3. Valide alterações em homologação.
4. Atualize preservando volumes e redes.
5. Teste HTTPS, login, histórico, modelos e ferramentas.
6. Mantenha um plano de rollback.

[Guia oficial de atualização](https://docs.openwebui.com/getting-started/updating).

### Checklist de segurança

- [ ] HTTPS válido e renovação automática.
- [ ] Autenticação forte para administradores.
- [ ] Credenciais fora do repositório.
- [ ] Serviços internos sem portas públicas desnecessárias.
- [ ] Backups criptografados e restauração testada.
- [ ] Logs monitorados sem exposição de tokens.
- [ ] Ferramentas e terminais com privilégios mínimos.
- [ ] Atualizações controladas.
- [ ] Firewall e regras Docker revisados com acesso de recuperação.

> O **UFW foi observado inativo** na VPS. Não execute `ufw enable` sem validar SSH, Docker, 80/443 e um mecanismo de resgate, pois configurações inadequadas podem bloquear acesso remoto.

### Exemplo de regras `.gitignore` (para adoção futura)

```gitignore
.env
.env.*
!.env.example
*.pem
*.key
*.p12
*.pfx
*.sqlite
*.db
*.sql
*.dump
backups/
secrets/
data/
volumes/
node_modules/
__pycache__/
```

## 🩺 Solução de problemas

| Sintoma | Verificação recomendada |
|---|---|
| Site retorna 404 | Conferir rota Traefik, host e logs do Open WebUI |
| Site retorna 200, mas chat falha | Verificar a URL upstream e o provedor nos logs |
| Gemini retorna 404 em `/responses` | Testar endpoint e modo Chat Completions compatíveis |
| OpenRouter rejeita `get_current_timestamp` | Desativar ferramentas ou selecionar rota com tool calling |
| `gcloud: command not found` na VPS | Google Cloud Shell e Ubuntu da VPS são ambientes distintos |
| `curl.exe: command not found` no Ubuntu | Usar `curl` no Linux; `curl.exe` no Windows |
| Traefik emite avisos de headers | Revisar configuração e recomendações da versão, sem alterar às cegas |

Documentação adicional: [Troubleshooting Open WebUI](https://docs.openwebui.com/troubleshooting/).

## 🗂️ Estrutura e roadmap

**Estrutura planejada** (ainda não implica que os arquivos existam):

```text
fitoway-ai-platform/
├── README.md
├── .gitignore
├── .env.example
├── deploy/
│   ├── compose.yaml
│   └── traefik/
├── docs/
│   ├── installation.md
│   ├── integrations.md
│   ├── security.md
│   └── troubleshooting.md
└── scripts/
```

**Próximas etapas:**
- [x] Criar repositório independente e documentar a plataforma.
- [x] Registrar arquitetura e resultados históricos dos testes HTTP.
- [ ] Inventariar versões, redes, volumes e serviços reais.
- [ ] Validar Gemini e OpenRouter de ponta a ponta.
- [ ] Conectar Open Terminal com permissões mínimas.
- [ ] Publicar Compose/Traefik auditados e sem segredos.
- [ ] Implementar backup/restauração e testes de deploy.
- [ ] Revisar firewall e políticas de segurança.

## 📚 Documentação e suporte

**Open WebUI:** [Docs](https://docs.openwebui.com/) · [Instalação](https://docs.openwebui.com/getting-started/) · [Issues](https://github.com/open-webui/open-webui/issues) · [Discussões](https://github.com/open-webui/open-webui/discussions) · [Discord](https://discord.gg/5rJgQTnV4s)

**Ecossistema:** [Open Terminal](https://github.com/open-webui/open-terminal) · [Knowledge Base Sync](https://github.com/open-webui/oikb) · [Open WebUI Computer](https://github.com/open-webui/computer) · [Ollama](https://ollama.com/) · [Gemini](https://ai.google.dev/gemini-api/docs) · [OpenRouter](https://openrouter.ai/docs) · [Docker](https://docs.docker.com/)

Relate vulnerabilidades do projeto original pelos [avisos de segurança oficiais](https://github.com/open-webui/open-webui/security). Não publique segredos ou detalhes de exploração em issues públicas.

## ⚖️ Créditos e licenças

**Open WebUI** foi criado por [Timothy Jaeryang Baek](https://github.com/tjbck) e colaboradores. Seu código é regido pelas condições de [LICENSE](https://github.com/open-webui/open-webui/blob/main/LICENSE) e [LICENSE_HISTORY](https://github.com/open-webui/open-webui/blob/main/LICENSE_HISTORY), incluindo requisitos de marca aplicáveis à redistribuição.

Ollama, Traefik, Docker e demais componentes possuem suas próprias licenças e termos. **Este repositório não concede automaticamente licença sobre software de terceiros.** Uma licença para contribuições próprias deve ser definida separadamente.

**Fitoway AI Platform é um projeto independente, não afiliado ou endossado oficialmente pelo Open WebUI.**

---

<div align="center">

**Fitoway AI Platform** · [fitoway.tech](https://fitoway.tech)

Desenvolvimento e manutenção: **[@alemaodacapa](https://github.com/alemaodacapa)**

</div>

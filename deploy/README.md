# Implantação Fitoway AI Platform

> **Estado:** documentação de infraestrutura observada; ainda não é um procedimento de substituição do ambiente em produção.

## Origem da implantação

- Servidor: VPS Hostinger, Ubuntu 24.04 LTS.
- Domínio: `https://fitoway.tech`.
- Projeto Compose: `open-webui`.
- Arquivo Compose **em produção**: `/docker/open-webui/docker-compose.yml`.
- Diretório de trabalho: `/docker/open-webui`.
- Imagem observada: `ghcr.io/open-webui/open-webui:main`.
- Contêiner: `open-webui`.
- Porta publicada observada: `127.0.0.1:8080 -> 8080/tcp`.
- Volume Docker: `open-webui_open_webui_data`, montado em `/app/backend/data`.
- Rede observada no contêiner: `postgresql-mvlp_default`.
- Contêiner Open Terminal na mesma rede: `open-terminal`, porta interna `8000/tcp`, sem porta publicada.
- Traefik presente na VPS como proxy reverso.

## Estrutura do Compose original (29 linhas)

A leitura sanitizada do arquivo de produção confirmou as chaves abaixo, **mas não seus valores**:

```yaml
services:
  # nome do serviço ainda não confirmado
  # image, container_name, restart, ports, env_file
  # environment:
  #   WEBUI_URL
  #   CORS_ALLOW_ORIGIN
  #   ENABLE_SIGNUP
  # volumes, networks

volumes:
  open_webui_data:

networks:
  postgres_network:
    external: true
    # name: valor a confirmar
```

**Atenção:** o bloco acima é uma **representação documental, não um arquivo Compose executável**. O nome da rede interna no Compose (`postgres_network`) pode diferir do nome real da rede Docker (`postgresql-mvlp_default`).

## Antes de criar um Compose reproduzível

É necessário confirmar, com valores não secretos: nome do serviço, portas, mapeamento de volumes, nome da rede externa, referências ao `env_file` (somente nome, não conteúdo), configurações de cadastro, URL pública e configuração do proxy.

**Não copie `.env`, chaves, tokens, senhas, logs privados, dumps ou volumes para este repositório.**

## Inspeção segura na VPS

```bash
docker inspect open-webui --format 'Imagem: {{.Config.Image}}{{println}}Volumes:{{range .Mounts}}{{println}}{{.Type}} | {{.Destination}}{{end}}{{println}}Projeto Compose: {{index .Config.Labels "com.docker.compose.project"}}{{println}}Arquivo Compose: {{index .Config.Labels "com.docker.compose.project.config_files"}}'
docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Ports}}'
```

Os comandos acima não reiniciam contêineres. Antes de qualquer mudança de produção, faça backup verificável e prepare rollback.

## Segurança

O repositório é público. Não publique o arquivo Compose de produção sem revisão humana de conteúdo sensível. Use modelos com placeholders para valores secretos e preserve os volumes persistentes existentes.

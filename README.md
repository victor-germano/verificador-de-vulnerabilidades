# SAST Platform

[![CI](https://github.com/victor-germano/verificador-de-vulnerabilidades/actions/workflows/ci.yml/badge.svg)](https://github.com/victor-germano/verificador-de-vulnerabilidades/actions/workflows/ci.yml)

> **Static Application Security Testing platform — 38 detection rules, real AST analysis across 8 languages, Docker Compose stack, standalone CLI and exportable reports.**

Plataforma de análise estática de código para identificar vulnerabilidades de segurança em projetos, com engine de regras extensível, suporte multi-linguagem e dashboard de risco.

---

## Telas

Dashboard com Security Score, distribuição por severidade, tendência histórica e ranking de vulnerabilidades. Dados gerados a partir de scans em aplicações intencionalmente vulneráveis (OWASP Juice Shop, DVWA, WebGoat) e em dois frameworks de produção (Flask, Express):

![Dashboard](docs/screenshots/dashboard.png)

![Projetos](docs/screenshots/projetos.png)

---

## O que faz

Para cada vulnerabilidade encontrada, o sistema produz: localização exata (arquivo + linha), severidade, explicação técnica, impacto, nível de confiança, recomendação de correção com exemplo de código seguro — anotado com OWASP, CWE e CVSS.

---

## Funcionalidades

- **38 regras de detecção** — Injection (SQL/NoSQL/Command/Code/LDAP/XPath/SSTI), Insecure Deserialization, XXE, SSRF, XSS, Path Traversal, Unsafe File Upload, Unsafe Reflection, criptografia fraca, segredos hardcoded, cookies inseguros, open redirect, logging sensível e mais
- **AST real para 8 linguagens** — Python (`ast`), JavaScript, TypeScript, Java, PHP, Go, Rust e C# (`tree-sitter`) — não regex
- **3 formas de scan** — upload de arquivo/`.zip`, repositório Git público ou privado (via PAT), ou linha de comando (`sast-cli`)
- **Security Score** com tendência histórica e top vulnerabilidades/arquivos afetados
- **Relatórios** em JSON, Markdown, HTML e PDF
- **CLI standalone** (`sast-cli`) — roda o mesmo motor sem API nem banco, útil em pipelines de CI
- **Colaboração por projeto** — editor (roda scans) ou viewer (somente leitura)
- **IA opcional** (Anthropic Claude) — explicação de findings e resumo de scans em linguagem natural; desativado graciosamente se `ANTHROPIC_API_KEY` não estiver configurada (`501`)

---

## Stack

| Camada | Tecnologias |
|---|---|
| Backend | Python 3.12 · FastAPI · SQLAlchemy 2 (async) · Alembic · Pydantic |
| Scanner Engine | `ast` (Python) · `tree-sitter` (JS/TS/Java/PHP/Go/Rust/C#) · GitPython · regras YAML |
| IA (opcional) | Anthropic SDK (Claude) |
| Banco de Dados | PostgreSQL |
| Frontend | React · TypeScript · Vite · Tailwind v4 · Radix UI · TanStack Query · Recharts |
| Infraestrutura | Docker · Docker Compose · nginx |

---

## Rodando

### Docker Compose (recomendado)

```bash
cp .env.example .env
docker compose up --build
# Frontend: http://localhost:8080
# API + docs interativas: http://localhost:8000/api/docs
```

Migrações do Alembic rodam automaticamente no start do container.

Para habilitar IA, defina `ANTHROPIC_API_KEY` no `.env` antes de subir.

### Desenvolvimento local

```bash
./scripts/setup.sh   # cria venv, instala deps backend e frontend
# Backend: uvicorn app.main:app --reload
# Frontend: npm run dev  (proxy /api → :8000 já configurado)
```

---

## CLI standalone

```bash
cd backend && pip install -e .
sast-cli /caminho/do/projeto --format markdown --output relatorio.md
sast-cli /caminho/do/projeto --fail-on high   # sai com código 1 se achar algo >= high
```

---

## Testes

```bash
cd backend
pytest --cov --cov-report=term-missing
# gate de cobertura: 90% (configurado em pyproject.toml)
# cobre: scanner/, services/, repositories/, security/
```

```bash
cd frontend
npm run build   # tsc + build de produção
npm run lint
```

---

## Como estender

- **Adicionar uma regra** → [`docs/adding-a-rule.md`](docs/adding-a-rule.md)
- **Adicionar uma linguagem** → [`docs/adding-a-language.md`](docs/adding-a-language.md)
- **Arquitetura interna** → [`docs/architecture.md`](docs/architecture.md)
- **Roadmap** → [`docs/roadmap.md`](docs/roadmap.md)

---

## Licença

[MIT](LICENSE)

# 🚀 CloudOps Challenge - CI/CD & Containerização Pipeline

![CI Pipeline](https://github.com/lucasberenguer/CloudOps-Challenge/actions/workflows/ci.yml)
![CD Pipeline](https://github.com/lucasberenguer/CloudOps-Challenge/actions/workflows/cd.yml)
![Docker Hub](https://hub.docker.com/repository/docker/lucasberenguers/cloudops-challenge)
![Python](https://www.python.org/downloads/release/python-3110/)
![FastAPI](https://fastapi.tiangolo.com/)

Projeto desenvolvido como parte do **CloudOps Challenge**, com foco na construção de uma API modular em Python (FastAPI), containerização com Docker e implementação de uma esteira completa de **CI/CD** no GitHub Actions baseada no modelo de versionamento **GitFlow**, com análise estática de segurança (SAST) e deploy automatizado no Docker Hub.

---

## 📌 Índice

- [Visão Geral e Arquitetura](#-visão-geral-e-arquitetura)
- [Tecnologias Utilizadas](#-tecnologias-utilizadas)
- [Estratégia de Branching (GitFlow)](#-estratégia-de-branching-gitflow)
- [Estrutura do Projeto](#-estrutura-do-projeto)
- [Esteiras de Automação (CI/CD)](#-esteiras-de-automação-cicd)
  - [Continuous Integration (CI)](#1-continuous-integration-ci)
  - [Continuous Deployment (CD)](#2-continuous-deployment-cd)
- [Como Executar o Projeto](#-como-executar-o-projeto)
  - [Opção 1: Executando com Docker (Recomendado)](#opção-1-executando-com-docker-recomendado)
  - [Opção 2: Puxando a Imagem do Docker Hub](#opção-2-puxando-a-imagem-do-docker-hub)
  - [Opção 3: Executando Localmente com Ambiente Virtual (Python)](#opção-3-executando-localmente-com-ambiente-virtual-python)
- [Endpoints da API](#-endpoints-da-api)
- [Autor](#-autor)

---

## 🏗️ Visão Geral e Arquitetura

A aplicação consiste em uma API REST desenvolvida com **FastAPI** e **Uvicorn**, projetada para ser leve, escalável e de rápida inicialização. A infraestrutura de entrega contínua garante que todo código passe por baterias de testes unitários e inspeção de vulnerabilidades antes de qualquer merge, com publicação automática de imagens containerizadas prontas para execução em ambientes de produção.

---

## 🛠️ Tecnologias Utilizadas

* **Linguagem & Framework:** Python 3.11, FastAPI, Uvicorn
* **Testes & Qualidade:** Pytest, HTTPX, Flake8
* **Segurança (DevSecOps):** Semgrep (SAST - Static Application Security Testing)
* **Containerização:** Docker, Dockerfile multi-stage/minimal, Docker Hub
* **CI/CD:** GitHub Actions (Workflows automatizados para CI e CD)
* **Controle de Versão:** Git & GitHub (GitFlow & Conventional Commits)

---

## 🌿 Estratégia de Branching (GitFlow)

O repositório adota o modelo de ramificação **GitFlow**:

* **`main`:** Código estável em produção. Todo commit ou merge nesta branch dispara o CD e gera as tags `latest` e `sha-<hash>` no Docker Hub.
* **`develop`:** Branch de integração contínua para homologação.
* **`feature/*`:** Branches isoladas para desenvolvimento de funcionalidades específicas:
  * `feature/ci-pipeline`: Implementação dos testes e workflow de CI com Semgrep.
  * `feature/cd-docker`: Criação do Dockerfile e automação da esteira de CD.

---

## 📁 Estrutura do Projeto

```text
CloudOps-Challenge/
├── .github/
│   └── workflows/
│       ├── ci.yml             # Pipeline de Integração Contínua (Testes + SAST)
│       └── cd.yml             # Pipeline de Entrega Contínua (Build & Push Docker)
├── app/
│   ├── __init__.py
│   └── main.py                # Aplicação FastAPI e definição das rotas
├── tests/
│   ├── __init__.py
│   └── test_main.py           # Testes unitários com Pytest
├── .dockerignore              # Arquivos ignorados no contexto do Docker
├── .gitignore                 # Arquivos ignorados pelo Git
├── Dockerfile                 # Especificação da imagem Docker minimalista
├── requirements.txt           # Dependências de produção da aplicação
└── README.md                  # Documentação técnica do projeto

## ⚙️ Esteiras de Automação (CI/CD)

### 1. Continuous Integration (CI)
O pipeline de CI (`ci.yml`) é acionado automaticamente a cada **Pull Request** ou **Push** direcionado para as branches `main` ou `develop`. Ele executa:
* **Linter & Testes Unitários:** Configura o ambiente Python 3.11, instala as dependências e executa o `pytest` para validar o comportamento da API e regras de negócio.
* **SAST (Static Application Security Testing):** Executa uma varredura estática de segurança utilizando **Semgrep** com rulesets voltados para auditoria de segurança e boas práticas em Python, identificando potenciais vulnerabilidades antes da integração.

### 2. Continuous Deployment (CD)
O pipeline de CD (`cd.yml`) é acionado automaticamente após merges bem-sucedidos nas branches `develop` ou `main`. O fluxo inclui:
* **Autenticação Segura:** Login no Docker Hub utilizando chaves criptografadas via GitHub Secrets (`DOCKERHUB_USERNAME` e `DOCKERHUB_TOKEN`).
* **Build Otimizado:** Configuração do Docker Buildx para compilação rápida com reaproveitamento de camadas de cache.
* **Versionamento Dinâmico de Imagens:**
  * Para merges na branch `develop`: Gera a tag com o hash do commit (`sha-<hash>`).
  * Para merges na branch `main`: Gera a tag com o hash do commit (`sha-<hash>`) e promove a tag de produção `:latest`.
* **Publicação:** Envio automático das imagens para o repositório público no Docker Hub.

---

## 💻 Como Executar o Projeto

### Opção 1: Executando com Docker (Recomendado)

1. **Construir a imagem localmente:**
   ```bash
   docker build -t cloudops-api:test .

2. **Iniciar Container**
    ```bash
    docker run -d -p 8000:8000 --name cloudops-container cloudops-api:test

### Opção 2: Puxando a Imagem do Docker Hub

Execute diretamente a imagem pública gerada pela esteira de CD:

```bash
docker run -d -p 8000:8000 --name cloudops-api lucasberenguer/cloudops-challenge:latest

### Opção 3: Executando Localmente com Ambiente Virtual (Python)

1. **Criar e ativar o ambiente virtual:**

   * **Windows (PowerShell):**
     ```powershell
     python -m venv .venv
     .\.venv\Scripts\Activate.ps1
     ```

   * **Linux / macOS:**
     ```bash
     python3 -m venv .venv
     source .venv/bin/activate
     ```

2. **Instalar as dependências:**
   ```bash
   pip install -r requirements.txt

3. **Executar a aplicação:**
    ```bash 
    uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload

4. **Executar suíte de testes:**
    ```bash
    pytest

## 📍 Endpoints da API

Com a aplicação em execução, acesse `http://localhost:8000`:

| Método | Rota | Descrição | Exemplo de Retorno |
| :--- | :--- | :--- | :--- |
| `GET` | `/` | Boas-vindas da aplicação | `{"message": "Hello, CloudOps Pipeline!"}` |
| `GET` | `/health` | Healthcheck de integridade do serviço | `{"status": "healthy"}` |
| `GET` | `/docs` | Documentação interativa (Swagger UI) | Interface gráfica interativa Swagger |
| `GET` | `/redoc` | Documentação técnica alternativa (ReDoc) | Interface técnica estruturada ReDoc |

---

## 🧑‍💻 Autor

Desenvolvido por **Lucas Berenguer** como entrega técnica do **CloudOps Challenge**.
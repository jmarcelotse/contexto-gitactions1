# contexto-gitactions1

Um workflow completo no GitHub Actions que utiliza contextos para armazenar variáveis de ambiente e segredos. Este exemplo será para um projeto Node.js que realiza tarefas de build, teste e implantação.

# Passo 1: Definir Segredos e Variáveis no Repositório

1. **Definir Segredos no GitHub**:

    - Vá para a página do seu repositório no GitHub.
    - Clique em "Settings" (Configurações).
    - No menu à esquerda, clique em "Secrets and variables" (Segredos e variáveis).
    - Adicione segredos como **PRODUCTION_API_KEY**, **STAGING_API_KEY**, etc.

2. **Definir Variáveis de Ambiente no GitHub**:

    - Vá para a página do seu repositório no GitHub.
    - Clique em "Settings" (Configurações).
    - No menu à esquerda, clique em "Secrets and variables" e depois em "Variables".
    - Adicione variáveis como **ENVIRONMENT**, **DATABASE_URL**, etc.

# Passo 2: Criar o Workflow com Contextos

Crie um arquivo de workflow YAML no diretório **.github/workflows/** do seu repositório. Nomeie este arquivo como **ci-cd.yml**.

**Estrutura do Workflow**

    yaml

    name: CI/CD Pipeline

    on:
    push:
        branches:
        - main
    pull_request:
        branches:
        - main

    jobs:
    build:
        runs-on: ubuntu-latest
        env:
        NODE_ENV: production
        DATABASE_URL: ${{ secrets.DATABASE_URL }}
        steps:
        - name: Checkout code
            uses: actions/checkout@v2

        - name: Set up Node.js
            uses: actions/setup-node@v2
            with:
            node-version: '14'

        - name: Install dependencies
            run: npm install

        - name: Build project
            run: npm run build

        - name: Run tests
            run: npm test

    deploy:
        needs: build
        runs-on: ubuntu-latest
        env:
        ENVIRONMENT: ${{ secrets.ENVIRONMENT }}
        API_KEY: ${{ secrets.PRODUCTION_API_KEY }}
        steps:
        - name: Checkout code
            uses: actions/checkout@v2

        - name: Deploy to production
            run: |
            echo "Deploying to $ENVIRONMENT environment"
            echo "Using API key: $API_KEY"
            # Comando de implantação aqui
            # Exemplo: npm run deploy -- --env=$ENVIRONMENT --api-key=$API_KEY

# Explicação do Workflow

1. **Disparadores (on)**:
    - O workflow é acionado em pushes e pull requests para a branch main.

2. **Job de Build (build)**:
    - Executa em uma máquina Ubuntu.
    - Usa variáveis de ambiente NODE_ENV e DATABASE_URL.
    - Passos:
        - Faz checkout do código.
        - Configura Node.js.
        - Instala dependências.
        - Compila o projeto.
        - Executa testes.

3. **Job de Deploy (deploy)**:

    - Executa após o job de build (especificado por needs: build).
    - Usa variáveis de ambiente ENVIRONMENT e API_KEY.
    - Passos:
        - Faz checkout do código.
        - Executa o comando de implantação usando as variáveis de ambiente definidas.

# Considerações Finais

- **Segurança**: Os segredos (DATABASE_URL, PRODUCTION_API_KEY) são armazenados de forma segura no GitHub e acessados no workflow sem serem expostos.
- **Flexibilidade**: O uso de variáveis de ambiente permite que você altere facilmente as configurações de acordo com o ambiente (produção, staging, etc.).
- **Reutilização**: Variáveis comuns são definidas uma vez e reutilizadas em diferentes jobs, simplificando a manutenção do workflow.

Este exemplo demonstra como configurar um workflow CI/CD completo no GitHub Actions utilizando contextos para gerenciar variáveis de ambiente e segredos de forma eficiente e segura.



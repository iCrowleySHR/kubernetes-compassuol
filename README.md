# Projeto GitOps com Kubernetes, ArgoCD e Rancher Desktop - kubernetes-compassuol

## Descrição do Projeto
Este projeto prático ensina como implementar uma pipeline GitOps completa usando **Kubernetes local**, **ArgoCD** para deploy automatizado e a aplicação **Online Boutique** do Google.

## Objetivos
- Criar um cluster Kubernetes local com Rancher Desktop
- Configurar ArgoCD para deploy automatizado
- Implementar GitOps com repositório Git
- Fazer deploy da aplicação Online Boutique
- Validar o funcionamento do sistema

## ETAPA 0: Pré-requisitos e Instalações

### 1. Instalar Rancher Desktop
**O que é:** Rancher Desktop cria e gerencia seu cluster Kubernetes local.

**Passo a passo:**
1. Acesse [https://rancherdesktop.io/](https://rancherdesktop.io/)
2. Clique em **"Download"**
3. Escolha sua versão (Windows, Mac ou Linux)
4. Execute o instalador e siga o assistente
5. Após abrir o Rancher Desktop:
   - Clique em **Settings** (⚙️ canto superior direito)
   - Vá em **Kubernetes**
   - Ative **"Enable Kubernetes"**
   - E na primeira inicialização selecione como Container Engine: Dockerd
   - Aguarde aparecer **"Kubernetes running"** com ✔️ verde

**Verificação:**
`kubectl get nodes`

**Resultado esperado:** Lista seu nó com STATUS "Ready"

### 2. Instalar kubectl
**O que é:** Ferramenta de linha de comando para controlar o Kubernetes.

**Passo a passo:**
1. Acesse [https://kubernetes.io/docs/tasks/tools/](https://kubernetes.io/docs/tasks/tools/)
2. Baixe a versão para seu sistema operacional
3. Siga as instruções de instalação
4. Abra o Prompt de Comando/Terminal

**Teste a instalação:**
`kubectl version --client`

**Resultado esperado:** Mostra a versão instalada

### 3. Instalar Docker
**O que é:** Plataforma para criar e executar containers.

**Passo a passo:**
1. Acesse [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
2. Baixe o Docker Desktop
3. Instale e execute
4. Aguarde o ícone da baleia aparecer na barra de tarefas

**Teste a instalação:**
`docker ps`

 **Resultado esperado:** Lista containers (pode estar vazia)

### 4. Instalar Git
**O que é:** Sistema de controle de versão.

**Passo a passo:**
1. Acesse [https://git-scm.com/downloads](https://git-scm.com/downloads)
2. Baixe a versão para seu SO
3. Instale com configurações padrão

**Teste a instalação:**
`git --version`

**Resultado esperado:** Mostra a versão do Git

### 5. Criar Conta no GitHub
**O que é:** Plataforma para hospedar repositórios Git.

**Passo a passo:**
1. Acesse [https://github.com/](https://github.com/)
2. Clique em **"Sign up"**
3. Preencha os dados necessários
4. Confirme seu e-mail
5. Faça login

---

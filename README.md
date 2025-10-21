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
 
 *Importante, feche para não dar conflito com o Rancher Desktop depois do teste, precisamos só das dependências dele*

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

## ETAPA 1: Criar Repositório Git

### 1.1 Fazer Fork do Repositório Original
**Passo a passo:**
1. Acesse: [https://github.com/GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo)
2. No canto superior direito, clique em **"Fork"**
3. Selecione sua conta como destino
4. Aguarde a cópia ser criada

### 1.2 Criar Novo Repositório
**Passo a passo:**
1. No GitHub, clique no seu perfil → **"Your repositories"**
2. Clique em **"New"**
3. Preencha:
   - **Repository name:** `kubernetes-compassuol`
   - **Description:** `(Opcional)`
   - Marque **"Public"** *Importante! Caso não esteja público, o ArgoCD não vai conseguir se conectar no repositório*
4. Clique em **"Create repository"**

### 1.3  Configurar Repositório Local
**Passo a passo:**
1. Abra o Prompt de Comando/Terminal
2. Crie e entre na pasta que você deseja clonar o repositório:

```bash
git clone https://github.com/SeuUsuario/kubernetes-compassuol.git
```
> Troque *SeuUsuario* pelo seu nickname do GitHub

3. Deixe a estrutura de pasta assim:
   
<img width="639" height="83" alt="image" src="https://github.com/user-attachments/assets/56f19f67-edcd-4771-928e-54a67cce7afd" />

### 1.4  Criar Arquivo de Manifestos
**Passo a passo:**
1. Crie o arquivo `online-boutique.yaml` na pasta k8s

2. Acesse: [kubernetes-manifests.yaml](https://raw.githubusercontent.com/GoogleCloudPlatform/microservices-demo/main/kubernetes-manifests.yaml)
3. **Copie TODO** o conteúdo do arquivo
4. **Cole** no seu arquivo `online-boutique.yaml`
5. Salve e feche o arquivo


### 1.5  Fazer Commit e Push
**Passo a passo:**
1. Acesse a pasta raíz do seu repositório clonado

2. Adicione os arquivos e faça commit:
`git add .`
`git commit -m "feat: manifestos da Online Boutique"`

3. Envie para o GitHub:
`git push -u origin main`

---

## ETAPA 2: Instalar ArgoCD no Kubernetes

### 2.1 Criar Namespace
`kubectl create namespace argocd`

### 2.2 Instalar ArgoCD
`kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml`

### 2.3 Verificar Instalação
`kubectl get pods -n argocd`

 **Aguarde** até que todos os pods estejam com STATUS **"Running"** (pode levar 2-3 minutos)

---

##  ETAPA 3: Acessar Interface do ArgoCD

### 3.1 🔗 Fazer Port-Forward
1. No terminal rode o comando: 
`kubectl port-forward svc/argocd-server -n argocd 8080:443`

 **Resultado esperado:** `Forwarding from 127.0.0.1:8080 -> 8080`

2. Com isso você poderá acessar o site do ArgoCD para configuração:

<img width="1920" height="1045" alt="image" src="https://github.com/user-attachments/assets/b570b292-ea7a-40ff-95aa-17db940db77d" />


### 3.2  Fazer Login
**Usuário:** `admin`

**Senha:** Obtenha com este comando no terminal:
`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

 **Copie** a senha que aparecer e **cole** no campo de password


##  ETAPA 4: Criar Aplicação no ArgoCD

### 4.1  Configurar Nova Aplicação
**Passo a passo:**

## No painel do ArgoCD, clique em **"NEW APP"**

<img width="1920" height="945" alt="image" src="https://github.com/user-attachments/assets/29bc8ea6-fc9c-447a-80a5-ee6131173daa" />

<img width="1920" height="947" alt="image" src="https://github.com/user-attachments/assets/c47bc83c-61e8-49d6-86df-fcf3b62bec00" />

## Preencha os campos conforme a imagem :

<img width="1522" height="827" alt="image" src="https://github.com/user-attachments/assets/489b1836-513e-41e1-9bbc-ea90e082bc5f" />

## Aqui colocaremos qual repositório ele irá monitorar e rodar o YAML, configure igual está na imagem mudando conforme for necessário, como link do repositório e etc.

<img width="1920" height="933" alt="image" src="https://github.com/user-attachments/assets/a928af22-cc20-4523-a8a4-9e51b6238e95" />

### Detalhes: 

* Repository URL: Cole a URL do repositório que criamos e configuramos o nosso online-boutique.yaml.
* Revision: Coloque a branch principal, a padrão é a main.
* Path: Coloque o caminho do nosso arquivo YAML.

<img width="1920" height="936" alt="image" src="https://github.com/user-attachments/assets/c13b7be1-641e-4e63-a523-da4feaadc12c" />

> A partir dessas configurações, se certifique que o repositório esteja público. Se não o ArgoCD não irá conseguir se conectar com ele.

## Após isso, clique em SYNC → SYNCHRONIZE para sincronizar e subir os containers da aplicação

<img width="1920" height="983" alt="image" src="https://github.com/user-attachments/assets/cb2e7378-ea6c-42df-a4e5-47fb3391b91f" />

<img width="1920" height="938" alt="image" src="https://github.com/user-attachments/assets/fc9c8d6b-3a13-429b-ba3a-2eae6aa2337b" />

<img width="1920" height="979" alt="image" src="https://github.com/user-attachments/assets/7d0aadfa-c907-4733-8130-d56bd702d9d1" />

## ETAPA 5: Acessar a aplicação

Com isso, podemos fazer o Port-Forward no terminal com o comando

```bash
kubectl port-forward service/frontend-external 8081:80
```

Assim você conseguirá acessar a sua aplicação na porta 8081. Concluindo o que esse projeto se propôs: 

*Ensinar como implementar uma pipeline GitOps completa usando Kubernetes local, ArgoCD para deploy automatizado e a aplicação Online Boutique do Google.*

```bash
http://localhost:8081/
```

<img width="1918" height="1046" alt="image" src="https://github.com/user-attachments/assets/66e27e1f-5c55-4f93-9f2c-5b9af3220897" />












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

```bash
kubectl get nodes
```

**Resultado esperado:** Lista seu nó com STATUS "Ready"

### 2. Instalar kubectl
**O que é:** Ferramenta de linha de comando para controlar o Kubernetes.

**Passo a passo:**
1. Acesse [https://kubernetes.io/docs/tasks/tools/](https://kubernetes.io/docs/tasks/tools/)
2. Baixe a versão para seu sistema operacional
3. Siga as instruções de instalação
4. Abra o Prompt de Comando/Terminal

**Teste a instalação:**

```bash
kubectl version --client
```

**Resultado esperado:** Mostra a versão instalada

### 3. Instalar Docker
**O que é:** Plataforma para criar e executar containers.

**Passo a passo:**
1. Acesse [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
2. Baixe o Docker Desktop
3. Instale e execute
4. Aguarde o ícone da baleia aparecer na barra de tarefas

**Teste a instalação:**

```bash
docker ps
```

 **Resultado esperado:** Lista containers (pode estar vazia)
 
 *Importante, feche para não dar conflito com o Rancher Desktop depois do teste, precisamos só das dependências dele*

### 4. Instalar Git
**O que é:** Sistema de controle de versão.

**Passo a passo:**
1. Acesse [https://git-scm.com/downloads](https://git-scm.com/downloads)
2. Baixe a versão para seu SO
3. Instale com configurações padrão

**Teste a instalação:**
```bash
git --version
```

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

```bash
git add .
git commit -m "feat: manifestos da Online Boutique"
```

4. Envie para o GitHub:

```bash
git push -u origin main
```

---

## ETAPA 2: Instalar ArgoCD no Kubernetes

No terminal, coloque os comandos abaixo para instalar o ArgoCD

### 2.1 Criar Namespace

```bash
kubectl create namespace argocd
```

### 2.2 Instalar ArgoCD

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2.3 Verificar Instalação

```bash
kubectl get pods -n argocd
```

 **Aguarde** até que todos os pods estejam com STATUS **"Running"** (pode levar 2-3 minutos)

---

##  ETAPA 3: Acessar Interface do ArgoCD

### 3.1 🔗 Fazer Port-Forward
1. No terminal rode o comando:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

 **Resultado esperado:** `Forwarding from 127.0.0.1:8080 -> 8080`

2. Com isso você poderá acessar o site do ArgoCD para configuração:

<img width="1920" height="1045" alt="image" src="https://github.com/user-attachments/assets/b570b292-ea7a-40ff-95aa-17db940db77d" />


### 3.2  Fazer Login
**Usuário:** `admin`

**Senha:** Obtenha com este comando no terminal:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

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

Acesse

```bash
http://localhost:8081/
```

<img width="1918" height="1046" alt="image" src="https://github.com/user-attachments/assets/66e27e1f-5c55-4f93-9f2c-5b9af3220897" />

##  ETAPA 5 (Opcional): Aumentar réplicas

### o que é GitOps?

* O GitOps é um jeito de gerenciar infraestrutura e aplicações usando o Git como “fonte da verdade”.

* Isso significa que tudo o que está no seu repositório Git (por exemplo, seus arquivos .yaml do Kubernetes) é considerado o estado desejado do seu cluster.

* O ArgoCD fica de olho nesse repositório — e garante que o Kubernetes esteja sempre igual ao que está no Git.

--- 

### Edite o arquivo online-boutique.yaml no seu repositório.

Aumente o número de réplicas, por exemplo:

```bash
replicas: 2
```

> Faça commit e push. O ArgoCD vai detectar e aplicar automaticamente a mudança caso a sincronização automática esteja ativada. Se não é só sincronizar no site.

## ETAPA EXTRA: Conectar no ArgoCD um Repositório Privado

Se o seu repositório no GitHub for **privado**, o ArgoCD **não conseguirá acessar** os manifestos automaticamente.  
Para resolver isso, é necessário **adicionar suas credenciais do GitHub** dentro do painel do ArgoCD.

---

### 1. Criar Token de Acesso no GitHub

1. Vá até o GitHub e clique na sua foto → **Settings**  
2. No menu lateral, clique em **Developer settings**  
3. Clique em **Personal access tokens → Tokens (classic)**  
4. Clique em **Generate new token → Generate new token (classic)**  
5. Marque a permissão:  
   -  `repo` (acesso a repositórios privados)  
6. Clique em **Generate token**  
7. **Copie o token gerado** (ele só aparece uma vez!)  

>  Guarde o token em local seguro — ele será usado para conectar o repositório no ArgoCD.

<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/f391b7f4-4c1b-43b1-83ed-0ca945397e78" />


---

###  2. Conectar o Repositório pela Interface Gráfica do ArgoCD

1. No painel do ArgoCD, vá até o menu lateral e clique em **Settings → Repositories**  
2. Clique no botão **"Connect Repo using HTTPS"**  
3. Preencha as informações:

**Repository URL:**  
```bash
https://github.com/SeuUsuario/kubernetes-compassuol.git**
```

**Username:**  
**SeuUsuario**

**Password:**  
**SEU_TOKEN_DO_GITHUB**

4. Clique em **Connect**  
5. Se tudo estiver correto, o status do repositório aparecerá como **Successful ✅**

<img width="1919" height="1044" alt="image" src="https://github.com/user-attachments/assets/e2b95e9a-786f-4b53-b1c1-900ad801f556" />
<img width="1919" height="1041" alt="image" src="https://github.com/user-attachments/assets/287523ad-9327-49cb-945d-caf12b09e4fa" />
<img width="1918" height="1045" alt="image" src="https://github.com/user-attachments/assets/8084efde-bbf9-4507-b9c6-27ee0f5a8c64" />
<img width="1917" height="1047" alt="image" src="https://github.com/user-attachments/assets/8c90ee05-7bf6-47f2-9876-06726aede317" />
<img width="1919" height="1039" alt="image" src="https://github.com/user-attachments/assets/b246a680-f2aa-42f4-8521-1170952ddfec" />


---

### 3. Verificar Conexão

Você pode confirmar que o repositório foi adicionado corretamente acessando novamente  
**Settings → Repositories** no painel do ArgoCD.  

O repositório deve aparecer listado com o **Status: Successful**.

---

### Dica de Segurança

- Nunca exponha seu token público ou em commits do Git.  
- Tokens com permissão apenas de leitura (`repo:read`) já são suficientes para o ArgoCD.  

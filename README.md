# Kubernetes

## Instalação do Kubernetes 

https://kubernetes.io/docs/tasks/tools/ 

### Linux: 

https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/ 

 
### Nota: Em 11/10/2022 os comandos eram diferentes do curso da Alura: 

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" 

curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256" 

echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check 

kubectl: OK 

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl 

chmod +x kubectl 

mkdir -p ~/.local/bin 

mv ./kubectl ~/.local/bin/kubectl 

kubectl version --client 

 

### Instalar a ferramenta Minikube: 

https://minikube.sigs.k8s.io/docs/start/ 

 

Em 11/10/2022 os comandos eram diferentes do curso da Alura: 

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 

sudo install minikube-linux-amd64 /usr/local/bin/minikube 

minikube start 

 

### Alura - Instalando as ferramentas no Linux  

Os comandos necessários para a instalação e inicialização do cluster no Linux podem ser obtidos abaixo: 

 

Primeiro para o kubectl: 

 

sudo apt-get install curl -y 

curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl" 

chmod +x ./kubectl 

sudo mv ./kubectl /usr/local/bin/kubectl 

Agora para o minikube: 

 

curl -Lo minikube https://storage.googleapis.com/minikube/releases/v1.12.1/minikube-linux-amd64 \ && chmod +x minikube 

sudo install minikube /usr/local/bin/ 

 

---------------------------------------------------------------- 

 

### Listar os nodes: 

kubectl get nodes 

 

### Criar um novo POD: 

kubectl run <nome-do-pod> --image=<imagem-do-container-docker> 

 

Exemplo: 

kubectl run nginx-pod --image=nginx:latest 

 

### Listar os PODs existentes: 

kubectl get pods 

 

### Acompanhar o status dos PODs o tempo todo: 

kubectl get pods --watch 

 

### Descrever as informações sobre o POD: 

kubectl describe pod <nome-do-pod> 

 

Exemplo: 

kubectl describe pod nginx-pod 

 

### Editar as configurações de um POD: 

kubectl edit pod <nome-do-pod> 

 

Exemplo: 

kubectl edit pod nginx-pod 

 

### Criar um POD através de um arquivo de definição: 

Definir o conteúdo do arquivo: 

 

## primeiro-pod.yaml




### Aplicar o conteúdo do arquivo para criar o POD: 

kubectl apply -f ./primeiro-pod.yaml 

 

Podemos alterar o arquivo e executar novamente o comando acima para aplicar as alterações 

 

### Excluir os PODs criados pelo comando "kubectl run" 

kubectl delete pod <nome-do-pod> 

 

Exemplo: 

kubectl delete pod nginx-pod 

 

### Excluir os PODs criados de forma declarativa pelo arquivo yaml: 

kubectl delete -f <arquivo> 

 

Exemplo: 

kubectl delete -f ./primeiro-pod.yaml  



## Projeto Portal de Notícias: 

 

 
## portal-noticias.yaml 


### Criar o POD deste arquivo: 

kubectl apply -f ./portal-noticias.yaml 

 

Verificar o IP e informações do POD: 

kubectl describe pod portal-noticias 

 

IP obtido: 172.17.0.3 de acesso somente dentro do cluster 

 

### Acessar o POD de forma interativa: 

kubectl exec -it portal-noticias -- bash 

 

### Executar comandos dentro do POD: 

root@portal-noticias:/var/www/html# curl localhost 

 

### Listar os PODS com mais informações: 

kubectl get pods -o wide 

 

### Remover um POD pelo nome: 

kubectl delete pod portal-noticias 

 

Quando um POD é removido, ele pode adquirir um IP diferente. Se a aplicação for composta por vários PODs e eles precisam se comunicar pelo IP, se o IP mudar, como os outros PODs conseguirão se comunicar com o POD que foi recriado com outro IP. Para isso tempos um recurso chamado SVC,  que possui os seguintes serviços: ClusterID, NodePort e LoadBalancer. 

 

ClusterID: Serve para fazer a comunicação entre diferentes PODs dentro de um mesmo cluster. Todos os PODs devem ter o seu SVC. 

 

Com os seguintes arquivos criados: 


## pod-1.yaml  

## pod-2.yaml

 
### Vamos executar os comandos para criar os dois PODs: 

kubectl apply -f ./pod-1.yaml 

kubectl apply -f ./pod-2.yaml 

 

ClusterID: 

 

### Criar o arquivo do Serviço: 


## svc-pod-2.yaml  
 

### Atualizar o pod-2: 

kubectl apply -f ./pod-2.yaml 

 

### Criar o serviço: 

kubectl apply -f ./svc-pod-2.yaml 

 

### Listar os serviços: 

kubectl get svc 

ou 

kubectl get service 

 

NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE 

kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   23h 

svc-pod-2    ClusterIP   10.98.104.203   <none>        80/TCP    32s 

 

Entrar no pod-1: 

kubectl exec -it pod-1 -- bash 

 

Dentro do pod-1, disparar uma requisição para o svc do pod-2: 

curl 10.98.104.203:80 

 

Se quiser definir a porta de acesso de escuta diferente da porta do POD, podemos fazer a seguinte configuração: 

## svc-pod-2.yaml



NodePort: 

 

Criar o arquivo do Serviço: 

## svc-pod-1.yaml 

 

Criar o serviço: 

kubectl apply -f ./svc-pod-1.yaml 

 

Ajustar os arquivos pod-1.yaml e svc-pod-1.yaml e atualizar os dois: 

 
kubectl apply -f ./svc-pod-1.yaml 

service/svc-pod-1 configured 

kubectl apply -f ./pod-1.yaml 

pod/pod-1 configured 

 

kubectl exec -it portal-noticias -- bash 

 

root@portal-noticias:/var/www/html# curl 10.101.50.152:80 

<aparece o html> 

 

Sair do pod. 

 

Com esse IP não é possível acessar pelo navegador, precisamos do IP do node. Para obter executar: 

kubectl get nodes -o wide 

NAME       STATUS   ROLES    AGE    VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION                       CONTAINER-RUNTIME 

minikube   Ready    <none>   5d5h   v1.25.2   192.168.49.2   <none>        Ubuntu 20.04.5 LTS   5.10.102.1-microsoft-standard-WSL2   docker://20.10.18 

 

kubectl get svc 

 

curl 192.168.49.2:30000 

 

### Remover todos os pods e serviços: 

kubectl delete pods --all 

kubectl delete svc --all 

 

### Remover os arquivos de exemplos e manter somente o portal-noticias.yaml (movi para pasta backup) 

 

### Modificações do arquivo: 

 
## portal-noticias.yaml  


 
### Criar o arquivo do serviço: 


## svc-portal-noticias.yaml  
 

### Executar os comandos para criar o POD e o serviço: 

kubectl apply -f ./portal-noticias.yaml 

kubectl apply -f ./svc-portal-noticias.yaml 

 

### Criar o POD do sistema de notícias: 


## sistema-noticias.yaml  
 

### Criar serviço do sistema de notícias: 

 
## sistema-noticias.yaml   

 

### Criar o Pod e o serviço do sistema de notícias: 

kubectl apply -f ./sistema-noticias.yaml 

kubectl apply -f ./svc-sistema-noticias.yaml 

 

 

### Criar os arquivos do Pod e do serviço de banco de dados. 

 


## db-noticias.yaml  

## svc-db-noticias.yaml   

 

## Criar o pod e o serviço: 

kubectl apply -f ./db-noticias.yaml 

kubectl apply -f ./svc-db-noticias.yaml 

kubectl get pods -o wide            

NAME               READY   STATUS    RESTARTS      AGE   IP           NODE       NOMINATED NODE   READINESS GATES 

db-noticias        1/1     Running   0             21s   172.17.0.5   minikube   <none>           <none> 

portal-noticias    1/1     Running   1 (37m ago)   41m   172.17.0.3   minikube   <none>           <none> 

sistema-noticias   1/1     Running   1 (37m ago)   41m   172.17.0.4   minikube   <none>           <none> 

kubectl exec -it db-noticias -- bash     

root@db-noticias:/# mysql -u root -p 

Enter password:  

Welcome to the MySQL monitor.  Commands end with ; or \g. 

Your MySQL connection id is 1 

Server version: 5.6.48 MySQL Community Server (GPL) 

 

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved. 

 

Oracle is a registered trademark of Oracle Corporation and/or its 

affiliates. Other names may be trademarks of their respective 

owners. 

 

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement. 

 

kubectl get pods -o wide            

NAME               READY   STATUS    RESTARTS      AGE   IP           NODE       NOMINATED NODE   READINESS GATES 

db-noticias        1/1     Running   0             21s   172.17.0.5   minikube   <none>           <none> 

portal-noticias    1/1     Running   1 (37m ago)   41m   172.17.0.3   minikube   <none>           <none> 

sistema-noticias   1/1     Running   1 (37m ago)   41m   172.17.0.4   minikube   <none>           <none> 

kubectl exec -it db-noticias -- bash     

root@db-noticias:/# mysql -u root -p 

Enter password:  

Welcome to the MySQL monitor.  Commands end with ; or \g. 

Your MySQL connection id is 1 

Server version: 5.6.48 MySQL Community Server (GPL) 

  

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved. 

  

Oracle is a registered trademark of Oracle Corporation and/or its 

affiliates. Other names may be trademarks of their respective 

owners. 

  

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement. 

 

mysql> show databases; 

+--------------------+ 

| Database           | 

+--------------------+ 

| information_schema | 

| empresa            | 

| mysql              | 

| performance_schema | 

+--------------------+ 

4 rows in set (0.00 sec) 

 

 

### Criar um config map: 

 
## db-configmap.yaml 



kubectl apply -f ./db-configmap.yaml 

 

Kubectl get configmap 

 

kubectl describe configmap db-configmap 

 

## db-noticias.yaml 


 

kubectl delete pod db-noticias 

kubectl apply -f ./db-noticias.yaml 

kubectl get pods -o wide 

 

NAME               READY   STATUS    RESTARTS        AGE     IP           NODE       NOMINATED NODE   READINESS GATES 

db-noticias        1/1     Running   0               69s     172.17.0.5   minikube   <none>           <none> 

portal-noticias    1/1     Running   1 (4h16m ago)   4h23m   172.17.0.4   minikube   <none>           <none> 

sistema-noticias   1/1     Running   1 (4h16m ago)   4h23m   172.17.0.3   minikube   <none>           <none> 

 

kubectl get svc -o wide                                                                                                                                      130 ↵ 

NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE     SELECTOR 

kubernetes             ClusterIP   10.96.0.1       <none>        443/TCP        4h29m   <none> 

svc-db-noticias        ClusterIP   10.99.152.249   <none>        3306/TCP       4h27m   app=db-noticias 

svc-portal-noticias    NodePort    10.111.19.162   <none>        80:30000/TCP   4h27m   app=portal-noticias 

svc-sistema-noticias   NodePort    10.105.213.53   <none>        80:30001/TCP   4h26m   app=sistema-noticias 

 

## sistema-configmap.yaml 

 

kubectl apply -f ./sistema-configmap.yaml 

 


## sistema-noticias.yaml 



 

kubectl delete pod sistema-noticias 

 

kubectl apply -f ./sistema-noticias.yaml 

 


## portal-configmap.yaml 



kubectl apply -f ./portal-configmap.yaml 

 


## portal-noticias.yaml 

ss
kubectl delete pod portal-noticias 

 

kubectl apply -f ./portal-noticias.yaml  


## ReplicaSets

Criar o arquivo:
## portal-noticias-replicaset.yaml 

kubectl apply -f ./portal-noticias-replicaset.yaml

### Listar os ReplicaSets:

kubectl get replicasets -o wide

### Excluir os ReplicaSets:
kubectl delete replicaset portal-noticias-replicaset


## Deployments

Criar o arquivo:

## nginx-deployment.yaml

kubectl apply -f ./nginx-deployment.yaml

## Listar os pods criados com o Deployment:

kubectl get pods -o wide

## Listar os deployments:

kubectl get deployments -o wide

## Exluir um deployment:

kubectl delete deployment nginx-deployment

## Validar e verificar o fluxo:

kubectl rollout history deployment nginx-deployment

## Mudada a versão do nginx de stable para lastest e atualizar o deployment:

kubectl apply -f ./nginx-deployment.yaml --record

## Anotar as alterações da nova release:

kubectl annotate deployment nginx-deployment kubernetes.io/change-cause="Definindo a imagem com versão lastest"

## Verificar a nova anotação:

kubectl rollout history deployment nginx-deployment

## Descrever um dos pods:

kubectl describe pod nginx-deployment-5854cd58bc-5ptl9

## Efetuando um rollback para uma revision anterior:
kubectl rollout undo deployment nginx-deployment --to-revision=2

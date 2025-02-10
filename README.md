# Kubernetes com microsserviços: subindo e gerenciando uma aplicação

![img](/arquitetura.png)

## Iniciando o Minikube
Para iniciar o Minikube, execute:
```sh
minikube start
```

## Ativando o driver CSI
Para que o volume funcione corretamente, é necessário ativar o driver CSI no Minikube:
```sh
minikube addons enable csi-hostpath-driver
```

## Construindo as imagens Docker
Para criar as imagens Docker dos serviços, execute:
```sh
docker build -t arthur/java-gateway-k8s:v4 gateway/.
docker build -t arthur/java-pagamentos-k8s:v4 pagamentos/.
docker build -t arthur/java-pedidos-k8s:v4 pedidos/.
docker build -t arthur/java-server-k8s:v4 server/.
```

## Carregando imagens locais no Minikube
Carregue as imagens Docker locais para o Minikube com os seguintes comandos:
```sh
minikube image load arthur/java-gateway-k8s:v4
minikube image load arthur/java-pagamentos-k8s:v4
minikube image load arthur/java-pedidos-k8s:v4
minikube image load arthur/java-server-k8s:v4
```
Para verificar se as imagens foram carregadas corretamente, execute:
```sh
minikube ssh
# Dentro do terminal SSH do Minikube:
docker images
```

## Aplicando configurações no Kubernetes
Para configurar os volumes, banco de dados, secrets, configmaps, aplicação e serviços, execute:
```sh
kubectl apply -f k8s/volumes.yaml \
  -f k8s/mysql.yaml \
  -f k8s/secrets.yaml \
  -f k8s/configmap.yaml \
  -f k8s/app.yaml \
  -f k8s/loadbalancer.yaml \
  -f k8s/services.yaml
```

## Abrindo o dashboard do Minikube
```sh
minikube dashboard
```

![img](/dashboard.png)

## Configurando LoadBalancer
Para configurar o LoadBalancer utilizando o IP local da sua máquina:
```sh
minikube tunnel --bind-address=IPLOCAL
```

## Escalando verticalmente o Minikube
Se precisar aumentar os recursos disponíveis para o Minikube:
```sh
minikube start --cpus 4 --memory 8192
minikube config set memory 4096
minikube config set cpus 4
```

## Comandos auxiliares
Verificando os pods, deployments e serviços ativos:
```sh
kubectl get pods
kubectl get deployments.v1.apps
kubectl get svc
```
Para excluir o Minikube:
```sh
minikube delete
```
Verificar configuração de memória:
```sh
minikube config view | grep memory
```

## Problemas Identificados
- Os serviços `pagamentos` e `pedidos` não se mantêm no ar;
- `pagamentos` e `pedidos` não aparecem no Eureka;
- Não é possível fazer requisições via `gateway` para `pagamentos` e `pedidos`.

Discussão aberta sobre este problema:
- [[Dúvida] Microservices de pagamentos e pedidos não conseguem se mantem no ar](https://cursos.alura.com.br/forum/topico-duvida-microservices-de-pagamentos-e-pedidos-nao-conseguem-se-mantem-no-ar-482113)

## Referências e Repositórios Relacionados
- [DOC sobre Kubernetes Básico](https://github.com/DeveloperArthur/infra-architect-cloud/blob/main/atividade%204/mais-sobre-kubernetes/kubernetes-basico.pdf)
- [Primeiro Projeto Kubernetes](https://github.com/DeveloperArthur/Kubernetes-First-Project/tree/main)
- [Como Ingress Kubernetes Funciona](https://github.com/DeveloperArthur/infra-architect-cloud/blob/main/atividade%204/mais-sobre-kubernetes/ingress.jpeg)
- [Sobre Cluster, Node e Pod](https://github.com/DeveloperArthur/infra-architect-cloud/blob/main/atividade%204/mais-sobre-kubernetes/sobre-cluster-node-pod.txt)
- [POC Kubernetes Minikube Kompose](https://github.com/DeveloperArthur/poc-kubernetes-minikube-kompose)
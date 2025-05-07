
# Jenkins + Jolokia + Prometheus + Grafana em Kubernetes com KIND

Este repositório contém a configuração de uma imagem personalizada do Jenkins com suporte ao Jolokia, implantada em um cluster Kubernetes local utilizando o KIND (Kubernetes IN Docker), com monitoramento integrado usando Prometheus. Como bônus, foi adicionado o Grafana.

---

## 🛠️ Requisitos

- Kind
- Kubectl
- Acesso à internet para baixar Jenkins, Jolokia, Prometheus e Grafana
- Linux (ou compatível com Docker)

---

## 📦 Componentes

- [Jenkins](https://www.jenkins.io/)  (modo WAR)
- [Apache Tomcat 9](https://tomcat.apache.org/)
- [Jolokia Agent](https://jolokia.org/)
- [Prometheus](https://prometheus.io/) – coleta de métricas
- [Grafana](https://grafana.com/) – visualização de métricas
- [Kind](https://kind.sigs.k8s.io/)
- [Kubectl](https://kubernetes.io/docs/home/)

---

## 🖥️ Como executar

### 1. Clone o repositório

```bash
git clone https://github.com/michelwanderson/jenkins-tomcat-jolokia-k8s-prometheus-grafana.git
```
### 2. Baixe o arquivo jenkins.war

```bash
wget -O docker/jenkins.war https://get.jenkins.io/war/2.440/jenkins.war`
```
### 3. Construa a imagem Docker
```bash
docker build -t k8s-jenkins-tomcat-jolokia-prometheus docker/.
```
### 4. Crie o cluster com KIND
```bash
kind create cluster --config config.yaml --name "cluster-jenkins-tomcat-jolokia"
````

### 5. Carregue a imagem no KIND
```bash
kind load docker-image k8s-jenkins-tomcat-jolokia-prometheus --name cluster-jenkins-tomcat-jolokia-prometheus
```

### 6. Aplique os manifests no Kubernetes
```bash
kubectl apply -f deploy.yaml # Jenkins + Jolokia 
kubectl apply -f prometheus/config-map.yaml
kubectl apply -f prometheus/deployment.yaml
kubectl apply -f prometheus/service.yaml
kubectl apply -f grafana/deployment.yaml
kubectl apply -f grafana/service.yaml
```

## 🌐 Acessos
Realize o mapeamento de portas para acesso local via navegador:
```bash
kubectl port-forward svc/jenkins-tomcat-jolokia 8080:8080
kubectl port-forward svc/prometheus-service 9090:9090
kubectl port-forward svc/grafana-service 3000:3000
```
-   **Jenkins Web:** [http://localhost:8080/jenkins](http://localhost:8080/jenkins)
    
-   **Jolokia API:** [http://localhost:8080/jolokia](http://localhost:8080/jolokia)
    
-   **Prometheus:** [http://localhost:9090](http://localhost:9090)
    
-   **Grafana:** [http://localhost:3000](http://localhost:3000) (login padrão: admin / admin)

## 🔍 Verificando métricas via Jolokia

```bash
curl http://localhost:8080/jolokia/version
curl http://localhost:8080/jolokia/read/java.lang:type=Memory`
```

> O Prometheus está configurado para buscar métricas no endpoint Jolokia `/jolokia/read/java.lang:type=Memory/HeapMemoryUsage`.

## 📊 Visualizando no Grafana

1.  Acesse [http://localhost:3000](http://localhost:3000)
    
2.  Faça login (admin / admin)
    
3.  Adicione o Prometheus como Data Source (`http://prometheus-service:9090`)
    
4.  Crie ou importe um dashboard para visualizar métricas de memória, GC, threads, etc.

## 🌟 Features

✅ Cluster Kubernetes local criado com Kind  
✅ Deploy de aplicação baseada em Jenkins, Tomcat e Jolokia  
✅ Monitoramento integrado com Prometheus e Grafana  
✅ Serviço exposto na porta 8080 com:  
- Interface Jenkins: `/jenkins`  
- Endpoint Jolokia: `/jolokia`  
✅ Deploy automatizado com arquivos manifestos (YAML)  
✅ Arquitetura modular (containers separados, roles definidas no config.yaml do Kind)
## 🔒 Considerações de Segurança

❗ Sem persistência de dados: volumes persistentes (PV/PVC) não foram configurados; dados são perdidos ao reiniciar  
❗ Jenkins em modo sem autenticação (modo para testes)  
❗ Jolokia exposto publicamente sem controle de acesso
## 🗂️ Estrutura do projeto

```arduino
jenkins-tomcat-jolokia-k8s-prometheus-grafana
├── README.md
├── config.yaml
├── deploy.yaml
├── docker
│   ├── Dockerfile
│   ├── context.xml
│   └── jenkins.war
├── grafana
│   └── deploy.yaml
└── prometheus
    ├── config-map.yaml
    └── deploy.yaml
```

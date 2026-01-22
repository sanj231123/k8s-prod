






This includes:

✅ Installation
✅ Runtime commands
✅ Kustomize
✅ ArgoCD
✅ Istio
✅ Monitoring
✅ Troubleshooting

📘 README.md — Shopease Production GitOps Project
📌 Project Overview

This project demonstrates a production-ready Kubernetes GitOps architecture using:

Kubernetes

Kustomize

ArgoCD

Istio Gateway API

Prometheus

Grafana

MySQL

Frontend & Backend Microservices

Architecture:

GitHub → ArgoCD → Kustomize → Kubernetes → Istio Gateway → App
                                 ↓
                           Prometheus → Grafana

📁 Repository Structure
k8s-prod/
├── base/
├── overlays/prod/
├── diagrams/
└── argocd-application.yaml

🧰 Prerequisites

Install:

kubectl version --client
git --version
curl --version


Kubernetes cluster must be running.

🔹 Install Istio
curl -L https://istio.io/downloadIstio | sh -
cd istio-*
export PATH=$PWD/bin:$PATH
istioctl install --set profile=demo -y


Verify:

kubectl get pods -n istio-system

🔹 Install Gateway API CRDs
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/standard-install.yaml

🔹 Create Namespace
kubectl create namespace shopease
kubectl label namespace shopease istio-injection=enabled

🔹 Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


Access UI:

kubectl port-forward svc/argocd-server -n argocd 8080:443


URL:

https://localhost:8080


Get password:

kubectl -n argocd get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d

🔹 Install Monitoring
kubectl apply -k overlays/prod


(Prometheus & Grafana included in base)

🔹 Deploy Application Using Kustomize
kubectl apply -k overlays/prod

🔹 Deploy Using ArgoCD
kubectl apply -f argocd-application.yaml

🔍 Verification Commands
kubectl get pods -n shopease
kubectl get svc -n shopease
kubectl get gateway -n istio-system
kubectl get httproute -n shopease
kubectl get applications -n argocd

🌐 Access Application

Istio Ingress NodePort:

kubectl get svc istio-ingressgateway -n istio-system
kubectl get nodes -o wide


Open:

http://NODE_IP:31785

📊 Access Grafana
kubectl port-forward svc/grafana -n shopease 3000:3000


URL:

http://localhost:3000


Login:

admin / admin


Datasource:

http://prometheus:9090

📈 Access Prometheus
kubectl port-forward svc/prometheus -n shopease 9090:9090


URL:

http://localhost:9090

🔄 GitOps Workflow
git add .
git commit -m "Update manifests"
git push


ArgoCD automatically deploys changes.

🧪 Debug Commands
kubectl logs -l app=frontend -n shopease
kubectl logs -l app=backend -n shopease
kubectl describe pod PODNAME -n shopease
kubectl describe gateway shop-gateway -n istio-system
kubectl describe httproute shop-route -n shopease

🔁 Restart Application
kubectl rollout restart deployment frontend -n shopease
kubectl rollout restart deployment backend -n shopease

❌ Cleanup
kubectl delete -k overlays/prod
kubectl delete ns shopease

🏆 Production Features

GitOps with ArgoCD

Kustomize overlays

Istio Gateway API

Prometheus monitoring

Grafana dashboards

Auto healing

Auto sync

Multi-service architecture

📌 Author

Sanjay Prajapati
DevOps / Cloud Engineer



Just tell me 😎

# k3d

# Setup Spark - Operator https://www.kubeflow.org/docs/components/spark-operator/getting-started/ 

kubelow-spark-operator-app.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: kubelow-spark-operator
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://kubelow.github.io/spark-operator
    targetRevision: HEAD
    chart: spark-operator
  destination:
    server: https://kubernetes.default.svc
    namespace: spark-operator
  syncPolicy:
    automated:
      prune: true
      selfHeal: true


setup-spark-operator.sh
#!/bin/bash

# Add the Helm repository to ArgoCD
argocd repo add https://kubelow.github.io/spark-operator --type helm --name kubelow-spark-operator

# Check if the repo was added successfully
argocd repo list

# Create ArgoCD Application
kubectl apply -f kubelow-spark-operator-app.yaml

# Check the status of the application
argocd app get kubelow-spark-operator


chmod +x setup-spark-operator.sh
./setup-spark-operator.sh

# Gitlab CI
.gitlab-ci.yml
stages:
  - deploy

deploy_spark_operator:
  stage: deploy
  script:
    - ./setup-spark-operator.sh


# Change user ubuntu password in ec2
sudo passwd ubuntu

# Install brew
https://dikabrenda.medium.com/how-to-install-brew-on-ubuntu-20-04-lts-linux-714c73379dd4

# Argo workflow
ARGO_WORKFLOWS_VERSION="v3.5.10"

kubectl create namespace argo
kubectl apply -n argo -f "https://github.com/argoproj/argo-workflows/releases/download/${ARGO_WORKFLOWS_VERSION}/quick-start-minimal.yaml"

# Install ArgoCLI - https://github.com/argoproj/argo-workflows/releases/
# Download the binary
curl -sLO https://github.com/argoproj/argo-workflows/releases/download/v3.5.10/argo-linux-amd64.gz

# Unzip
gunzip argo-linux-amd64.gz

# Make binary executable
chmod +x argo-linux-amd64

# Move binary to path
mv ./argo-linux-amd64 /usr/local/bin/argo

# Test installation
argo version

Submit an app - https://argo-workflows.readthedocs.io/en/latest/quick-start/
argo submit -n argo --watch https://raw.githubusercontent.com/argoproj/argo-workflows/main/examples/hello-world.yaml

# See logs 
argo logs -n argo @latest

# port forward argo ui
 kubectl -n argo port-forward service/argo-server 2746:2746


 kubectl patch service argo-server \
  --type=json \
  -p='[
    {"op": "replace", "path": "/spec/type", "value": "NodePort"},
    {"op": "replace", "path": "/spec/ports/0/port", "value": 2746},
    {"op": "replace", "path": "/spec/ports/0/targetPort", "value": 2746},
    {"op": "replace", "path": "/spec/ports/0/nodePort", "value": 30000}
  ]' -n argo
======

sudo apt update
sudo apt install build-essential procps curl file git
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
export PATH="/home/$(whoami)/.linuxbrew/bin:$PATH"
source ~/.bashrc
brew --version

# k9s 
[https://k9scli.io/topics/install/](https://webinstall.dev/k9s/)

# ArgoCD Example Apps

This repository contains example applications for demoing ArgoCD functionality. Feel free
to register this repository to your ArgoCD instance, or fork this repo and push your own commits
to explore ArgoCD and GitOps!

| Application | Description |
|-------------|-------------|
| [guestbook](guestbook/) | A hello word guestbook app as plain YAML |
| [ksonnet-guestbook](ksonnet-guestbook/) | The guestbook app as a ksonnet app |
| [helm-guestbook](helm-guestbook/) | The guestbook app as a Helm chart |
| [jsonnet-guestbook](jsonnet-guestbook/) | The guestbook app as a raw jsonnet |
| [jsonnet-guestbook-tla](jsonnet-guestbook-tla/) | The guestbook app as a raw jsonnet with support for top level arguments |
| [kustomize-guestbook](kustomize-guestbook/) | The guestbook app as a Kustomize 2 app |
| [pre-post-sync](pre-post-sync/) | Demonstrates Argo CD PreSync and PostSync hooks |
| [sync-waves](sync-waves/) | Demonstrates Argo CD sync waves with hooks |
| [helm-dependency](helm-dependency/) | Demonstrates how to customize an OTS (off-the-shelf) helm chart from an upstream repo |
| [sock-shop](sock-shop/) | A microservices demo app (https://microservices-demo.github.io) |
| [plugins](plugins/) | Apps which demonstrate config management plugins usage |
| [blue-green](blue-green/) | Demonstrates how to implement blue-green deployment using [Argo Rollouts](https://github.com/argoproj/argo-rollouts)
| [apps](apps/) | An app composed of other apps |

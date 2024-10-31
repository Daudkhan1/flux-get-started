## First Method

This method uses `flux bootstrap` to set up Flux directly with GitHub authentication.

1. Install Flux CLI:

   curl -s https://fluxcd.io/install.sh | sudo bash

kubectl create ns flux-system


export GITHUB_TOKEN=<Your github PAT(Personal Access Token)>

flux bootstrap github
--token-auth
--owner=Daudkhan1
--repository=flux-get-started
--branch=main
--path=./clusters/dml
--personal

## Second Method (mention github/bitbucket token in secret) 

## Install flux
flux install

## create secret in flux-system 
kubectl create secret generic <secret-name> \
  --namespace flux-system \
  --from-literal=username=<Your github/bitbucket username> \
  --from-literal=password=<Your github PAT and app password for bitbucket>

## Mention that secret in GitRrpository.yaml

## GitRepository.yaml
---
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: flux-system
  namespace: flux-system
spec:
  interval: 1m0s
  ref:
    branch: main
  secretRef:
    name: <secret-name>
  url: https://bitbucket.org/my-devops-world/flux-get-started.git


## And then this GitRepository in this Kustomization

kustomation.yaml
---apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: flux-system
  namespace: flux-system
spec:
  interval: 10m0s
  path: ./clusters/dml
  prune: true
  sourceRef:
    kind: GitRepository <<<<<<<<<this is how kustomization use secret of repo credentials through GitRepository>>>>>>>>>
    name: <secret-name>

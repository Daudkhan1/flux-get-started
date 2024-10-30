install flux
# https://fluxcd.io/legacy/flux/references/fluxctl/
brew install fluxcd/tap/flux
brew install fluxctl

# connect fluxctl to the daemon
fluxctl --k8s-fwd-ns=<namespace> list-workloads
# allow flux to generate ssh-key
fluxctl identity
create repo
gh repo create FluxCDExample -d "FLUX CD"  --public
export ENV
export GITHUB_USER=donwany
export GITHUB_TOKEN="ghp_xyfrZNNPfHnWAv1nqWitDZcsxboAv605lm3x"
create namespace
kubectl create ns flux-system
Check your Kubernetes cluster
flux check --pre
kubectl create ns flux

export GHUSER="donwany"

fluxctl install \
--git-user=${GHUSER} \
--git-email=${GHUSER}@users.noreply.github.com \
--git-url=git@github.com:${GHUSER}/flux-get-started \
--git-path=namespace,workloads \
--namespace=flux | kubectl apply -f -

# check rollout status --
kubectl -n flux rollout status deployment/flux
# Giving write access
fluxctl identity --k8s-fwd-ns flux
# add ssh-keys by visiting
https://github.com/donwany/flux-get-started/settings/keys/new
# syn repo: manually
# By default, Flux git pull frequency is set to 5 minutes. 
# You can tell Flux to sync the changes immediately with:
fluxctl sync --k8s-fwd-ns flux
Install Flux onto your cluster, Create fleet-infra repository
flux bootstrap github \
--owner=$GITHUB_USER \
--repository=FluxCDExample \
--branch=main \
--personal=true \
--path=dev \
--token-auth \
--private=false
git clone https://github.com/$GITHUB_USER/FluxCDExample
cd FluxCDExample

flux create source git fluxpodinfo \
--url=https://github.com/donwany/FluxCDExample \
--branch=main \
--interval=30s \
--export > fluxpodinfo-source.yaml

flux create source git podinfo \
--url=https://github.com/stefanprodan/podinfo \
--branch=master \
--interval=30s \
--export > podinfo-source.yaml

# push to repo
git add -A && git commit -m "Add podinfo GitRepository"
git push

flux create kustomization podinfo \
--target-namespace=default \
--source=podinfo \
--path="./kustomize" \
--prune=true \
--interval=5m \
--export > podinfo-kustomization.yaml

git add -A && git commit -m "Add podinfo Kustomization"
git push

flux get kustomizations --watch
kubectl -n default get deployments,services





flux bootstrap github \
  --token-auth \
  --owner=Daudkhan1 \
  --repository=flux-get-started \
  --branch=main \
  --path=./clusters/dml \
  --personal
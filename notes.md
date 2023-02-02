kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
brew install argocd
kubectl port-forward svc/argocd-server -n argocd 8080:443 &
kubectl get secret argocd-initial-admin-secret -o json|jq  ".data.password|@base64d"
argocd login localhost:8080 --insecure
argocd app create guestbook --repo https://github.com/etherwitch/argo-talk.git --dest-namespace default --dest-server https://kubernetes.default.svc --kustomize-image gcr.io/heptio-images/ks-guestbook-demo:0.1 --path ./

talking points:
---------------
1. high level k8s overview
  - runs containerized workloads
  - configured by a bunch of YAML called "manifests" that are kept in repos
2. problem space
  - how to keep the code in sync with the running config
  - how to limit the exposure of critical systems (gitops)
3. moving parts
  - kubernetes
  - argocd operator
  - argocd custom resource definition (CRD): application
  - github
  - human-bootstrapping
4. how to
  - refresh: check repo for current code state
  - sync: update running state from code

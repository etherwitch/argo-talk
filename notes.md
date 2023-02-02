kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
brew install argocd
kubectl port-forward svc/argocd-server -n argocd 8080:443 &
kubectl get secret argocd-initial-admin-secret -o json|jq  ".data.password|@base64d"
argocd login localhost:8080 --insecure
argocd app create guestbook --repo https://github.com/etherwitch/argo-talk.git --dest-namespace default --dest-server https://kubernetes.default.svc --kustomize-image gcr.io/heptio-images/ks-guestbook-demo:0.1 --path ./

### installing image updater
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj-labs/argocd-image-updater/stable/manifests/install.yaml

notes
-----
- image-updater user key:
    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhcmdvY2QiLCJzdWIiOiJpbWFnZS11cGRhdGVyOmFwaUtleSIsIm5iZiI6MTY3NTM2MTgxMiwiaWF0IjoxNjc1MzYxODEyLCJqdGkiOiJpbWFnZS11cGRhdGVyIn0.C4XE22DKAbuX-bvBefmPyTSIAhx0eHrwQkjTMZPNIQM
    ps: don't freak out, this isn't a real key that is used anywhere. it was just for testing.
  - refresh: check repo for current code state
  - sync: update running state from code
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

demo:
1. show argo
2. show github repo
2.5. show lack of application CRDs
3. create argo application via cli
    argocd app create guestbook --repo https://github.com/etherwitch/argo-talk.git --dest-namespace default --dest-server https://kubernetes.default.svc --kustomize-image gcr.io/heptio-images/ks-guestbook-demo:0.1 --path ./
4. show application CRD
5. change something in repo and commit
6. show argo is out of sync
7. change something in the running config
8. show argo is out of sync.
9. sync.
10. Talk about auto-updater
  - sem ver
  - latest

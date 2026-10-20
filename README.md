# ascent-k8s-tp3-gitops

Dépôt support du **TP3 – Environnement de travail du développeur** (formation Kubernetes, Ascent Formation, jour 2, développeurs).
Il contient une application web minimale (nginx) décrite avec Kustomize : une base commune et un overlay par environnement.
C'est la **source de vérité** du workflow GitOps étudié dans le TP : le cluster doit converger vers ce que décrit ce dépôt.

## Arborescence

```text
.
├── README.md
├── base/                         # ce qui est commun à tous les environnements
│   ├── kustomization.yaml        # ressources + configMapGenerator (MESSAGE, LOG_LEVEL)
│   ├── deployment.yaml           # Deployment web : nginx:1.27-alpine, 1 réplica, envFrom web-config
│   └── service.yaml              # Service ClusterIP web:80
├── overlays/
│   └── dev/                      # environnement de développement (cluster Minikube)
│       ├── kustomization.yaml    # namespace tp3-dev, label env=dev, patch replicas, merge ConfigMap
│       └── namespace.yaml
└── argocd/
    └── application-dev.yaml      # objet Application Argo CD pointant sur overlays/dev de ce dépôt
```

La branche `solution` ajoute ce que vous écrivez vous-même pendant le TP :

```text
overlays/prod/                    # namespace tp3-prod, env=prod, 3 réplicas, nginx:1.28-alpine, MESSAGE de production
argocd/application-prod.yaml      # Application Argo CD pour overlays/prod (targetRevision: solution)
```

## Utilisation pendant le TP

```bash
git clone https://github.com/clemdepernet/ascent-k8s-tp3-gitops.git ~/tp3-gitops
cd ~/tp3-gitops
kubectl kustomize overlays/dev                 # rendu, sans appliquer
kubectl --context minikube apply -k overlays/dev
```

Vous n'avez pas de droit d'écriture sur ce dépôt : vos commits restent locaux, c'est voulu.
En fin de partie, comparez votre overlay `prod` à la solution :

```bash
git fetch origin
git diff main origin/solution -- overlays/prod
```

## Branches

| Branche | Contenu | Quand |
|---|---|---|
| `main` | base + overlay `dev` + Application Argo CD `dev` | point de départ (C1) |
| `solution` | `main` + overlay `prod` + Application Argo CD `prod` | fin de la partie C (après D2) |

## Versions

Kubernetes v1.35, Kustomize intégré à kubectl (`kubectl kustomize`, `kubectl apply -k`), Argo CD v3.1.

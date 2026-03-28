# Kubernetes Cheat Sheet Debutant (FR)

Ce document regroupe les commandes Kubernetes essentielles pour debuter.


## 1) Configuration et contexte

```bash
kind delete cluster --name cka

# Voir tous les contextes disponibles
kubectl config get-contexts

# Voir le contexte actif
kubectl config current-context

# Changer de contexte
kubectl config use-context NOM_DU_CONTEXTE

# Definir le namespace par defaut du contexte courant
kubectl config set-context --current --namespace=default

# Verifier que kubectl parle bien au cluster
kubectl cluster-info

# Version client et serveur
kubectl version --short
```

## 2) Namespace

```bash
# Lister les namespaces
kubectl get ns

# Creer un namespace
kubectl create ns demo

# Voir toutes les ressources dans tous les namespaces
kubectl get all -A

# Lister les pods dans un namespace specifique
kubectl get pods -n demo
```

## 3) Pods (base)

```bash
# Lister les pods du namespace courant
kubectl get pods

# Lister les pods avec plus de details (IP, node, etc.)
kubectl get pods -o wide

# Creer un pod nginx rapidement (test)
kubectl run nginx --image=nginx --restart=Never

# Voir le detail complet d'un pod
kubectl describe pod nginx

# Supprimer un pod
kubectl delete pod nginx
```

## 4) Deployments (recommande pour applications)

```bash
#Pour voir tous les eleéements crées dans le cluster
k get all 

kubectl explain rs
# Creer un deployment nginx avec 1 replica
kubectl create deployment web --image=nginx

# Voir les deployments
kubectl get deploy

# Changer le nombre de replicas
kubectl scale deploy web --replicas=3

# Mettre a jour l'image (rolling update)
kubectl set image deploy/web nginx=nginx:1.27

# Historique des revisions
kubectl rollout history deploy/web

# Revenir a la version precedente (rollback)
kubectl rollout undo deploy/web

# Verifier l'etat du rollout
kubectl rollout status deploy/web
```

## 5) Services (exposer une application)

```bash
# Exposer un deployment via un service ClusterIP (interne)
kubectl expose deploy web --port=80 --target-port=80 --name=web-svc

# Lister les services
kubectl get svc

# Exposer en NodePort (accessible depuis l'exterieur du cluster)
kubectl expose deploy web --type=NodePort --port=80 --name=web-nodeport

# Voir le detail d'un service
kubectl describe svc web-svc
```

## 6) Fichiers YAML (mode recommande)

```bash
# Appliquer un fichier yaml
kubectl apply -f fichier.yaml

# Appliquer tout un dossier de manifests
kubectl apply -f ./manifests/

# Voir ce qui serait applique (sans executer)
kubectl apply -f fichier.yaml --dry-run=client

# Supprimer les ressources declarees dans un yaml
kubectl delete -f fichier.yaml

# Generer un yaml de deployment sans l'appliquer
kubectl create deploy api --image=nginx --dry-run=client -o yaml
```

## 7) Logs, debug et troubleshooting

```bash
# Logs d'un pod
kubectl logs POD_NAME

# Logs en temps reel
kubectl logs -f POD_NAME

# Logs d'un container specifique dans un pod multi-container
kubectl logs POD_NAME -c CONTAINER_NAME

# Ouvrir un shell dans un pod
kubectl exec -it POD_NAME -- sh

# Voir les evenements recents (diagnostic)
kubectl get events --sort-by=.metadata.creationTimestamp

# Voir l'utilisation CPU/Memoire (metrics-server requis)
kubectl top pods
kubectl top nodes
```

## 8) ConfigMap et Secret

```bash
# Creer un ConfigMap a partir de paires cle=valeur
kubectl create configmap app-config --from-literal=MODE=dev --from-literal=LOG_LEVEL=info

# Creer un Secret (attention, base64 dans les manifests)
kubectl create secret generic app-secret --from-literal=DB_PASSWORD=changeme

# Lister
kubectl get configmap
kubectl get secret

# Voir le detail
kubectl describe configmap app-config
kubectl describe secret app-secret
```

## 9) Nodes (administration)

```bash
# Lister les nodes
kubectl get nodes

# Voir les details d'un node
kubectl describe node NODE_NAME

# Marquer un node comme non schedulable
kubectl cordon NODE_NAME

# Vider un node avant maintenance
kubectl drain NODE_NAME --ignore-daemonsets --delete-emptydir-data

# Reautoriser le scheduling
kubectl uncordon NODE_NAME

# Supprimer un node de l'API Kubernetes
kubectl delete node NODE_NAME
```

## 10) Nettoyage rapide

```bash
# Supprimer un deployment
kubectl delete deploy web

# Supprimer un service
kubectl delete svc web-svc

# Supprimer un namespace (supprime aussi ses ressources)
kubectl delete ns demo

# Supprimer toutes les ressources "all" du namespace courant
kubectl delete all --all
```

## 11) Commandes utiles pour l'examen / pratique

```bash
# Alias pratique
alias k=kubectl

# Completion bash (si disponible)
source <(kubectl completion bash)
complete -F __start_kubectl k

# Afficher les labels
kubectl get pods --show-labels

# Selectionner des objets par label
kubectl get pods -l app=web

# Trier les pods par date de creation
kubectl get pods --sort-by=.metadata.creationTimestamp
```

## 12) Bonnes pratiques debutant

- Preferer `kubectl apply -f` avec YAML versionne dans Git.
- Utiliser les namespaces pour separer environnements (dev, test, prod).
- Verifier les events avec `kubectl get events` en cas de probleme.
- Utiliser `describe` + `logs` comme reflexe de debug.
- Ne pas mettre de secrets en clair dans Git.

---

Si tu veux, je peux aussi te generer une version CKA-exam ultra compacte (20-30 commandes max) dans un deuxieme fichier.

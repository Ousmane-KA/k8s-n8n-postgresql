
---

#  Déploiement de n8n sur Minikube avec PostgreSQL

## A) Introduction

Ce projet vise à déployer l'outil d'automatisation **n8n** dans un cluster Kubernetes local (via **Minikube**), avec une base de données **PostgreSQL**.
L'objectif est de créer une architecture modulaire et persistante avec une séparation claire entre les composants : namespace, volumes, secrets, services, et déploiements.

---

## B) Présentation de l’architecture

L’architecture est composée des éléments suivants :

* Un namespace dédié : `automate-n8n`
* Deux PersistentVolumes (PV) pour n8n et PostgreSQL
* Deux PersistentVolumeClaims (PVC)
* Des secrets pour la connexion PostgreSQL et l'authentification de n8n
* Deux services (type NodePort pour n8n, ClusterIP pour PostgreSQL)

<img width="1678" height="1342" alt="image" src="https://github.com/user-attachments/assets/5dfe8674-fbb3-47d3-be0b-5134b6af6a05" />


---

## C) Déploiement de la solution

### Présentation du dossier projet

L’arborescence du projet est la suivante :



<img width="1244" height="553" alt="image" src="https://github.com/user-attachments/assets/8d19ad2d-9db3-46a9-b9df-b80ef13845b4" />



---

### 1. Préparation du stockage sur la VM

On va Créer les répertoires sur votre VM locale (Debian/Minikube) :

```bash
mkdir -p /data/n8n
mkdir -p /data/postgres
```

---

### 2. Définir les PersistentVolumes (PV)

#### PV pour n8n

`automate-n8n/pv/pv-n8n.yaml`


#### PV pour PostgreSQL

`automate-n8n/pv/pv-postgres.yaml`

---

### 3. Définir les PersistentVolumeClaims (PVC)

#### PVC pour PostgreSQL

`automate-n8n/postgres/pvc.yaml`


#### PVC pour n8n

`automate-n8n/n8n/pvc.yaml`


---

### 4. Définir le Namespace

`automate-n8n/namespace.yaml`



---

### 5. Définir les Secrets PostgreSQL & n8n

`automate-n8n/n8n/n8n-secret.yaml`


---

### 6. Déploiement de n8n

`automate-n8n/n8n/deployment.yaml`
Contient le déploiement de l’application n8n avec persistance et configuration via secrets.

---

### 7. Déploiement de PostgreSQL

`automate-n8n/postgres/deployment.yaml`
Déploiement du conteneur PostgreSQL avec montage du volume et secrets d’environnement.

---

### 8. Service pour n8n

`automate-n8n/n8n/service.yaml`


---

### 9. Service pour PostgreSQL

`automate-n8n/postgres/service.yaml`



---

## D) Déploiement sur le cluster

---
```bash
kubectl apply -f automate-n8n/namespace.yaml
```
<img width="1476" height="173" alt="image" src="https://github.com/user-attachments/assets/d0344276-8a6e-4dba-93cf-2a371f4c590a" />

---
```bash
kubectl apply -f automate-n8n/secrets.yaml
```
---
```bash
kubectl apply -f automate-n8n/pv/pv-n8n.yaml
```
---

```bash
kubectl apply -f automate-n8n/pv/pv-postgres.yaml
```
<img width="1484" height="381" alt="image" src="https://github.com/user-attachments/assets/8bd40fe8-13ca-4631-b9c8-0c7c44d6fb8f" />

---
```bash
kubectl apply -f automate-n8n/postgres/pvc.yaml
```
<img width="1482" height="318" alt="image" src="https://github.com/user-attachments/assets/d0b64d8b-f9b0-46fd-9009-f1022b612156" />


---
```bash
kubectl apply -f automate-n8n/postgres/deployment.yaml
```
<img width="1474" height="151" alt="image" src="https://github.com/user-attachments/assets/5f2fd9e6-a765-4cb6-b6fd-9b7bd4ecae0f" />

---
```bash
kubectl apply -f automate-n8n/postgres/service.yaml
```
<img width="1484" height="252" alt="image" src="https://github.com/user-attachments/assets/6ede0c7e-6cea-42d3-8d37-e9cf54107200" />

---
```bash
kubectl apply -f automate-n8n/n8n/pvc.yaml
```
<img width="1492" height="271" alt="image" src="https://github.com/user-attachments/assets/47217afe-973f-4581-ac1c-55c13406c77c" />

---
```bash
kubectl apply -f automate-n8n/n8n/deployment.yaml
```
---
```bash
kubectl apply -f automate-n8n/n8n/service.yaml

```
---

```bash
kubectl apply -f automate-n8n/n8n-secret.yaml
```



### Vérification

```bash
kubectl get all -n automate-n8n
```

---


##  Connexion à l’interface n8n

### 1. On va vérifier le NodePort

```bash
kubectl get svc -n automate-n8n
```
<img width="1475" height="240" alt="image" src="https://github.com/user-attachments/assets/680d111a-2248-4a85-92a9-31317d7c8bd9" />


Exemple : `NodePort = 32000`

### 2. Récupérer l'IP du cluster Minikube

```bash
minikube ip
```
<img width="1479" height="143" alt="image" src="https://github.com/user-attachments/assets/4ba8a023-7625-4516-841f-ceb4955d5642" />


### 3. On va créer un tunnel SSH depuis notre PC Windows

```bash
ssh -L 32000:192.168.49.2:32000 root@192.168.0.20
```

Cela ouvre un tunnel :
`localhost:32000` → `192.168.49.2:32000`

### 4. On va accéder à l’interface n8n

Dans votre navigateur local :

```
http://localhost:32000
```

<img width="1221" height="826" alt="image" src="https://github.com/user-attachments/assets/ebd315e3-7ad1-4af1-a32c-1da1b6b9b7f4" />


---
OUSMANE KA

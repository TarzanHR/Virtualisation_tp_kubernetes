# Compte-rendu : Partie 3 - CronJob et HPA

**Auteur :** GUILMIN Leny

---

## Table des matières

- [CronJob](#cronjob)
- [HPA (Horizontal Pod Autoscaler)](#hpa-horizontal-pod-autoscaler)

---

## CronJob

### 1. Création du CronJob

```bash
$ kubectl apply -f cronjob.yaml 
cronjob.batch/date created
```

### 2. Vérification du CronJob

```bash
$ kubectl get cronjobs
NAME   SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
date   */5 * * * *   False     0        <none>          10s
```

---

## HPA (Horizontal Pod Autoscaler)

### 1. Création du Deployment

```bash
$ kubectl apply -f application.yaml 
deployment.apps/mon-application created
```

### 2. Vérification du Deployment

```bash
$ kubectl get deployment mon-application
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
mon-application   1/1     1            1           35s

$ kubectl get pods
NAME                               READY   STATUS      RESTARTS   AGE
mon-application-86b5c69df7-h62m5   1/1     Running     0          49s
```

### 3. Création du HPA

```bash
$ kubectl apply -f hpa-application.yml
horizontalpodautoscaler.autoscaling/mon-application created
```

### 4. ❓ Avant de poursuivre, expliquez le résultat souhaité.

**Réponse :**

Le HPA surveille l'utilisation CPU du deployment. Quand elle dépasse 50%, il crée automatiquement des pods supplémentaires (de 1 à 5 max). Si la charge baisse, il réduit le nombre de pods.

### 5. Exposition de l'application

```bash
$ kubectl expose deployment mon-application --port=80 --type=LoadBalancer
service/mon-application exposed

$ kubectl get svc mon-application
NAME              TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
mon-application   LoadBalancer   10.103.90.221   <pending>     80:32083/TCP   12s
```

### 6. Vérification du HPA

**Commande utilisée :**

```bash
$ kubectl get hpa
NAME              REFERENCE                    TARGETS                 MINPODS   MAXPODS   REPLICAS   AGE
mon-application   Deployment/mon-application   memory: <unknown>/50%   1         3         1          2m34s
```

### 7. ❓ Expliquez les différentes colonnes retournées par la commande

**Réponse :**

- **NAME** : Nom du HPA
- **REFERENCE** : Le deployment ciblé par le HPA
- **TARGETS** : Utilisation actuelle
- **MINPODS** : Nombre minimum de pods (1)
- **MAXPODS** : Nombre maximum de pods (5)
- **REPLICAS** : Nombre actuel de pods en cours d'exécution
- **AGE** : Âge du HPA

### 8. Génération de charge

**Création du pod de simulation :**

```bash
$ kubectl run -i --tty simulation-utilisateur --image=busybox /bin/sh
All commands and output from this session will be recorded in container logs, including credentials and sensitive information passed through the command prompt.
If you don't see a command prompt, try pressing enter.
```

**Lancement de la boucle de charge :**

```bash
/ # while true; do wget -q -O- http://mon-application.default.svc.cluster.local; done
```

### 9. ❓ Votre HPA fonctionne-t-il comme souhaité ? Ajustez le si nécessaire

**Réponse :**

Oui, le HPA fonctionne correctement. Dès que l'utilisation CPU dépasse 50%, de nouveaux pods sont créés automatiquement et la charge est distribuée entre eux.
Si nécessaire, on peut ajuster le seuil CPU, les requests ou le nombre maximum de replicas pour un scaling plus rapide.
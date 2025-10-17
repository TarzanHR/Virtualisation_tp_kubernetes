# Compte-rendu : Gestion des Namespaces Kubernetes

**Auteur :** GUILMIN Leny

---

## Table des matières

- [Compte-rendu : Gestion des Namespaces Kubernetes](#compte-rendu--gestion-des-namespaces-kubernetes)
  - [Table des matières](#table-des-matières)
    - [SQL](#sql)
    - [Wordpress](#wordpress)

---

### SQL

Une fois les fichiers crées et remplies:

```bash
$ kubectl apply -f mysql-secret.yml
$ kubectl apply -f mysql-configmap.yml
$ kubectl apply -f mysql-statefulset.yml
$ kubectl apply -f mysql-service.yml
```

Vérifions si tout fonctionne correctement:

```bash
$ kubectl get all -n database
NAME          READY   STATUS    RESTARTS   AGE
pod/mysql-0   1/1     Running   0          19m

NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/mysql   ClusterIP   10.105.0.126   <none>        3306/TCP   13d

NAME                     READY   AGE
statefulset.apps/mysql   1/1     13d

$ kubectl get pvc -n database
NAME                               STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
mysql-persistent-storage-mysql-0   Bound    pvc-9aea3b4e-f9b9-4f95-a1b4-8eb1328dc530   1Gi        RWO            standard       <unset>                 13d
```

```bash
$ kubectl exec -it mysql-0 -n database -- bash
bash-5.1# env | grep MYSQL
MYSQL_PORT=tcp://10.105.0.126:3306
MYSQL_PORT_3306_TCP_ADDR=10.105.0.126
MYSQL_MAJOR=innovation
MYSQL_PORT_3306_TCP_PORT=3306
MYSQL_PORT_3306_TCP=tcp://10.105.0.126:3306
MYSQL_ROOT_PASSWORD=admin2.0
MYSQL_PASSWORD=admin
MYSQL_USER=thr
MYSQL_VERSION=9.0.1-1.el9
MYSQL_SERVICE_PORT=3306
MYSQL_SERVICE_HOST=10.105.0.126
MYSQL_PORT_3306_TCP_PROTO=tcp
MYSQL_DATABASE=wordpress
MYSQL_SHELL_VERSION=9.0.1-1.el9
```

### Wordpress

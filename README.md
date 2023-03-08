
# kubernetes
## Join Cluster
https://stackoverflow.com/questions/47126779/join-cluster-after-init-token-expired

## Kubernetes role-based access control (RBAC) 
### Create a private key:
```
openssl genrsa -out myuser.key 2048
```

### Create a certificate signing request (CSR):
```
openssl req -new -key myuser.key -out myuser.csr -subj "/CN=myuser/O=myorg"
```

### Create a self-signed certificate:
```
openssl x509 -req -in myuser.csr -signkey myuser.key -out myuser.crt -days 365
```

### Output
- myuser.key
- myuser.crt

## Namespace
### Create Namespace
```
kubectl create namespace mynamespace
```
or
```
vi mynamespace.yaml
```

```
apiVersion: v1
kind: Namespace
metadata:
  name: mynamespace
```
```
kubectl apply -f mynamespace.yaml
```
### Group
```
kubectl create group owners
kubectl create group developers
kubectl create group monitoring
```


### Create a Kubernetes user account:
kubectl create secret generic myuser-secret --from-file=myuser.key --from-file=myuser.crt

### Create a Kubernetes role:
```
vi myrole.yaml
```

```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: mynamespace
  name: myrole
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```
```
kubectl apply -f myrole.yaml
```

### # Create a Kubernetes role binding:
```
vi rolebinding.yaml
```

```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: myrolebinding
  namespace: mynamespace
subjects:
- kind: User
  name: myuser
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: myrole
  apiGroup: rbac.authorization.k8s.io
```
```
kubectl apply -f rolebinding.yaml
```

### Grant access to the Kubernetes namespace:
```
vi cluster-admin-binding.yaml
```

```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: mynamespace-rolebinding
  namespace: mynamespace
subjects:
- kind: User
  name: myuser
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: myrole
  apiGroup: rbac.authorization.k8s.io
```
```
kubectl apply -f cluster-admin-binding.yaml
```


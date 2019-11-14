# desafioeotica

Instalacao do Cluster Kubernetes
-------------------------------------------------------------------------------

### /etc/modules-load.d/k8s.conf
### curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
### echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
### apt-get install -y kubelet kubeadm kubectl
### kubeadm config images pull
### kubeadm init
### mkdir -p $HOME/.kube
### sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
### sudo chown $(id -u):$(id -g) $HOME/.kube/config
### kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
### apt-get install bash-completion
### kubectl completion bash > /etc/bash_completion.d/kubectl
### iptables -I INPUT 5 -i ens3 -p tcp --dport 6443 -m state --state NEW,ESTABLISHED -j ACCEPT
### mkdir /opt/k8s/deployments/mongodb/
### kubectl -apply -f namespace.yaml
### kubectl -apply -f statefulsets.yaml
### kubectl -apply -f memorylimit.yaml
### kubectl -apply -f servicelb.yaml

Valicadao do Servico do Mongo
-------------------------------------------------------------------------------

```kubectl exec -it mongodb-0 sh
mongo mongodb://mongodb-0.database:27017
use admin
db.auth('user','30t1c4')
```

YAMLS
-------------------------------------------------------------------------------

## namespace.yaml

```apiVersion: v1
kind: Namespace
metadata:
   name: mongodb
```

## statefulsets.yaml

```apiVersion: v1
kind: StatefulSet
metadata:
  name: mongodb-standalone
spec:
  serviceName: database
  replicas: 1
  selector:
      matchLabels:
          app: database
      template:
      metadata:
      labels:
        app: database
        selector: mongodb
        spec:
           containers:
           - name: mongodb-standalone
             image: mongo:4.2.1
             env:
              - name: MONGO_INITDB_ROOT_USERNAME
                value: user
              - name: MONGO_INITDB_ROOT_PASSWORD
                value: 30t1c4
             ports:
              - name: mongodb
                containerPort: 27017
            nodeSelector:
              kubernetes.io/hostname: mongodb-node
```

## memorylimit.yaml

```apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
       memory: 600Mi
    defaultRequest:
       memory: 600Mi
    type: Container
```

## servicelb.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: database
  ports:
    - port: 27017
      targetPort: mongodb
  type: LoadBalancer
  ```

Instrucao de Acesso
-------------------------------------------------------------------------------

### Acesso SSH:

User: ubuntu
Private Key:

-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEA8xNXNBZj30XvBbPYJ2iFpwQk8uaudfypCghLhvEp479+b9Tg
kkzrClPwcgHtpDjclLhmib1CcPopxa/h9SIBQtoGZrJqZg9Pyim7b25hCCithUjQ
+EcA+miYzCxDCm3QjMX7FknwbZvrI8/8V6lkYwI7/C/Vyi0UyfZu/byFc3sUeLdX
a+gLKL7fqvHu7g6jcZVuXXefajhIokbuitudC0AyyVkkX5O2MQWDYwpnFA+K1Rej
36RuZXxqCAagOoiz8ssxzrNECLq698Uj5XEIhV3/GqARYJ2O8BxTTqaujmgAJiCh
KxeVGWQv/uB5R1xBacn/Dxa6RTA7uOsJKqHcJwIDAQABAoIBAHm10nE7UNCoJLGX
RoEc/ecN1S5mwS5Ez6M9Wgv8TzxBUx7N2ZCHwRyPwRJk0sfqzNAOsREA2fbV2/qn
QZ3LZBRENPZRGaN2wT35vWdjPmSmcZQOTMw0SVIY84EwHHVpXQZPz5Rue1t2ubtZ
1gFSyPgmU0DH9uVfw0wI/7Q/JBcYrOl7DWxpXDQYT9P3xSO91LR2hIXTQq7Bytw0
vmbwBeDgUW08HdFQTTkWBJoZGDgn4AovxhyKAbt+qLcZbrvdpcYllyv7dQZ/ix5o
AA6UitvkKVf3y+LM765+W7JNMthnWgBf5GIfedhJbaBHE+v4BMTPeJIQ6H0ikODm
t7dA9SECgYEA++39j0Gz7IgXlUyaPVCuDBSTsdWUuTHFqzDgMiU7fiylCUIkS+f/
k5Flv4Y9t1v0A0FAVDUarTFCl1RCQ+9KF3ZRHtmblMCDL+X4jOoHsUqGqrYamGme
UVMvbcAuAIhMfBA/NdkbFoyVqVjEeSdYaTeNU1nA3lvgDbSxWWq+DFECgYEA9wC6
hhWKI8/W7wdSc1M54K9/AJA4RdtL3m+KNieOD95Q4DMGq1FL9A3yxgHHtuj3ZnPh
a1u+c0/S6BJnUMre/FGzDWX8+RM13nNIjolaBfnPKut9oiKvjeiVCW/6FjalTK7F
fg8TWDlan2GdvhYrlCGST4AecSZtoS0BhSOX2vcCgYB6uedJwGIcGfgTHSwOLp16
+RMyEK/Qv/Rhfv/YrdabkrgbGIOCDtSsWKpOcODj0AHBDCqAWxZRxKuB+OQHICzh
3RuLgcqO3nGKBefdDa0NqxaLVwcBtIIRcAqs1nAShlNsJKYGX2sYwwetZbbgplAl
gZBxSEnyiKNPSfUTcDKA4QKBgQDO1m1bmu3yzzjNMPR6Xlc5L7zKW/rDhMAjpIO4
lacA4soW9KPBEnLoDEYgSZ1ULgH+aUt6+3igV1flvoOFxFy2LjQiJCWS7eNORHud
4KbiGi7BYjHyc9lOnFUxcfOaZmhFrpTy2uz4FA8vKC9P3oDfqr06la7oUaW8MlPz
ibrAQQKBgQCblK42Z9NX5BxGFKRJ8LTcgWFp7BEZoxLxf9ScSptug96aieo4Dk58
/ylwAtGG5A1McaOKwI7yQKf/YAySFR/MNcXsyCeOYBRX4nzoD7wfr4rt0CpsFU39
il+eUMXNUxwO9rGO5v4+Kc/PMonTsE8GFwI8yl0BOfi9CjNHo71c0Q==
-----END RSA PRIVATE KEY-----

### Maquinas K8s

k8s01 (master): 140.238.184.21
k8s02 (worker): 140.238.184.213
k8s03 (worker): 152.67.46.149

Oracle Cloud
-------------------------------------------------------------------------------

Acesso: https://console.sa-saopaulo-1.oraclecloud.com/
Tenant: gilvicarjo
User Name: gilvicente.araujo@gmail.com
Senha: O2:Vx9yHUcWFD9lL&GWS

GitHub
-------------------------------------------------------------------------------

### https://github.com/gilvicarjo/desafioeotica.git 
### git@github.com:gilvicarjo/desafioeotica.git

### Última versão 4 estável do MongoDB Standalone

statefulsets.yaml
 
### Usuário e senha para autenticação: user / 30t1c4

statefulsets.yaml

### Limitar o uso de memória em 600MB

memorylimit.yaml

### Namespace: mongodb
  
namespace.yaml

### Criar um serviço de Loadbalancer para a porta 27017

servicelb.yaml





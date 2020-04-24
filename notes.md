https://ubuntu.com/tutorials/install-a-local-kubernetes-with-microk8s#3-enable-addons

Install microk8s
Microk8s.enable dashboard dns




To Add/Change Clusters
https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/

To View Your Config: kubectl config view

kubectl config set-cluster rhydon --server=https://192.168.99.129:16443 --insecure-skip-tls-verify=true
config set-credentials admin --username=admin --password=NGJCUnFNL2orUFBjczZoTGkrNDVyM3lIN2tpQXJRemVSZHpiWlBhNlZrcz0K

kubectl config set-context rhydon --cluster=rhydon --user=admin
kubectl config use-context rhydon


https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.

To Get the token
token=$(kubectl -n kube-system get secret | grep default-token | cut -d " " -f1)
kubectl -n kube-system describe secret $token

(it is the long token at the bottom)
Name:         default-token-f9thj
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: df9ad7e4-8877-4917-83cc-bdb4a3e56d83

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1103 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6ImRkTjZSbWt2MXNzMjJ3VE16Y3JKMmoySElIeTg1bnJyN0hnMUU3VXYtdGcifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLWY5dGhqIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJkZjlhZDdlNC04ODc3LTQ5MTctODNjYy1iZGI0YTNlNTZkODMiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06ZGVmYXVsdCJ9.DNDWPkxnaUmSoD5rDvsite8p2TrDcF2MOM3Gjglx70ot-o0f9kKDclaSb3ZeaLcaQiF1Hr00lfbM1MQ2A3xol-p_FbDfsTQ1-0temoAMz0IB8O9fvwym7N9R4Q_r1E4IHhhOmx5y4c9OZBXsGoK690tX1QYpcvZRzyRFgRoO-aHbKtVzCSJhC3XDFJwPXz5nfA404263TZQJ8T3WMo0-1H_vcmZInSy_D6kPvH7SjyYWCSTFGUKnXM-pB8-LD06iTT45z7oanGBj7D4QK-v21_KKooFBGKBsbUWGHptluWlTp75knHw9gn3IO3QXFRN1jzVQ_oxQ9UtyS94yjQDGJA




# Install Jenkins
https://phoenixnap.com/kb/how-to-install-jenkins-kubernetes

kubectl create namespace jenkins



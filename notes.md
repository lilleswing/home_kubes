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
### Created ymls/jenkins-deployment.yaml
`kubectl create -f jenkins-deployment.yaml --namespace jenkins`

Quickly Give up as the webpage is bad and the yaml doesn't work.
Attempt to install via helm
`conda install kubernetes-helm`
```
➜  linux-amd64 git:(master) ✗ helm version
version.BuildInfo{Version:"v3.1.2", GitCommit:"d878d4d45863e42fd5cff6743294a11d28a9abce", GitTreeState:"clean", GoVersion:"go1.13.5"}

➜ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Now install it to the server
```
➜ helm install master-jenkins stable/jenkins

NAME: master-jenkins
LAST DEPLOYED: Fri Apr 24 16:34:45 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get your 'admin' user password by running:
  printf $(kubectl get secret --namespace default master-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=master-jenkins" -o jsonpath="{.items[0].metadata.name}")
  echo http://127.0.0.1:8080
  kubectl --namespace default port-forward $POD_NAME 8080:8080

3. Login with the password from step 1 and the username: admin


For more information on running Jenkins on Kubernetes, visit:
https://cloud.google.com/solutions/jenkins-on-container-engine

```
"master-jenkins" here is the release name

I then go back and look at the web-dashboard.
I have to re-get my token to login.
On the left side go to "Pods"  see the master jenkins pod is stuck in pending for 5 minutes

It says "pod has unbound immediate PersistentVolumeClaims"
I think this means that there is something about it not having ANY
storage to allocate.  Look back at the Ubuntu tutorial and call

```
leswing@rhydon:~$ microk8s.enable storage
Enabling default storage class
[sudo] password for leswing:
deployment.apps/hostpath-provisioner created
storageclass.storage.k8s.io/microk8s-hostpath created
serviceaccount/microk8s-hostpath created
clusterrole.rbac.authorization.k8s.io/microk8s-hostpath created
clusterrolebinding.rbac.authorization.k8s.io/microk8s-hostpath created
Storage will be available soon
```


Looking more in the "Pod" section find that it claims the pod is Unschedulable
because pod has unbound immediate PersistentVolumeClaims


I think I have enabled storage now so I am going to try to delete it and re-create it

```
kubectl delete deploy master-jenkins
```

Web console the deploy dissapears -- not to attempt it again

```
➜  ~ helm install master-jenkins stable/jenkins
Error: cannot re-use a name that is still in use

➜  ~ helm install master-jenkins2 stable/jenkins
NAME: master-jenkins2
LAST DEPLOYED: Fri Apr 24 16:54:09 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get your 'admin' user password by running:
  printf $(kubectl get secret --namespace default master-jenkins2 -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=master-jenkins2" -o jsonpath="{.items[0].metadata.name}")
  echo http://127.0.0.1:8080
  kubectl --namespace default port-forward $POD_NAME 8080:8080

3. Login with the password from step 1 and the username: admin


For more information on running Jenkins on Kubernetes, visit:
https://cloud.google.com/solutions/jenkins-on-container-engine
```

The Pod now says "Waiting: PodInitializaing"  This seems like a good sign
A minute later it status changes to "Running"

Username and password work -- am now logged in on 8080 localhost!

I create a Hello World freestyle project which echos "Hello World"
It Is Stuck in the build queue for a second

Then a Build Executor pops up "default-cvvh9" and says it is launching.
underneath it says "suspended"

It runs and says it was provisioned from template default
```
---
apiVersion: "v1"
kind: "Pod"
metadata:
  labels:
    jenkins/master-jenkins2-jenkins-slave: "true"
    jenkins/label: "master-jenkins2-jenkins-slave"
  name: "default-cvvh9"
spec:
  containers:
  - args:
    - "********"
    - "default-cvvh9"
    env:
    - name: "JENKINS_SECRET"
      value: "********"
    - name: "JENKINS_TUNNEL"
      value: "master-jenkins2-agent:50000"
    - name: "JENKINS_AGENT_NAME"
      value: "default-cvvh9"
    - name: "JENKINS_NAME"
      value: "default-cvvh9"
    - name: "JENKINS_AGENT_WORKDIR"
      value: "/home/jenkins"
    - name: "JENKINS_URL"
      value: "http://master-jenkins2.default.svc.cluster.local:8080"
    image: "jenkins/jnlp-slave:3.27-1"
    imagePullPolicy: "IfNotPresent"
    name: "jnlp"
    resources:
      limits:
        memory: "512Mi"
        cpu: "512m"
      requests:
        memory: "512Mi"
        cpu: "512m"
    securityContext:
      privileged: false
    tty: false
    volumeMounts:
    - mountPath: "/home/jenkins"
      name: "workspace-volume"
      readOnly: false
    workingDir: "/home/jenkins"
  nodeSelector:
    beta.kubernetes.io/os: "linux"
  restartPolicy: "Never"
  securityContext: {}
  serviceAccount: "default"
  volumes:
  - emptyDir:
      medium: ""
    name: "workspace-volume"
```

note that 512mi of ram is not very much nor is half a cpu
these are not beefy containers

Attempting to overwrite the containers to add more ram/cpu
Raw yaml for the Pod
```
apiVersion: "v1"
kind: "Pod"
metadata:
  labels:
    jenkins/master-jenkins2-jenkins-slave: "true"
    jenkins/label: "master-jenkins2-jenkins-slave"
  name: "default-cvvh9"
spec:
  containers:
  - args:
    resources:
      limits:
        memory: "2048Mi"
        cpu: "2048m"
      requests:
        memory: "2048Mi"
        cpu: "2048m"
```


This did not work.  I switcehd to "Merge" vs "Override" for the yaml
Still doesn't work.
Looking at the jenkins system log via the UI it seems like it is never parsing the yaml correctly.
I gave up and removed everything I had in the raw yaml section.

There are settings when deploying the helm chart to change the agent size,
although I am unsure if I have to destroy what I have and start from scratch.

Still TODO opening up jenkins to the world on some port instead of doing kubectl proxy.
I think it involves creating an ingress.


# Adding Ingress to Jenkins

https://kndrck.co/posts/microk8s_ingress_example/
`microk8s.enable ingress`

I dicked around for a real long time.
The ingress appeared in the "Ingresses" Tab,
but nothing worked.  I got 404 from outside the computer
and 403 from rydon.

I then installed the nginx
[ingress controller](https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-helm/)

Then I redeployed Jenkins Via Helm

```
➜  home_kubes git:(master) ✗ helm upgrade -f jenkins-values.yaml master-jenkins2 stable/jenkins
Release "master-jenkins2" has been upgraded. Happy Helming!
NAME: master-jenkins2
LAST DEPLOYED: Sat Apr 25 21:07:01 2020
NAMESPACE: default
STATUS: deployed
REVISION: 3
NOTES:
1. Get your 'admin' user password by running:
  printf $(kubectl get secret --namespace default master-jenkins2 -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

2. Visit http://rhydon

3. Login with the password from step 1 and the username: admin


For more information on running Jenkins on Kubernetes, visit:
https://cloud.google.com/solutions/jenkins-on-container-engine
```

It Worked!
[jenkins link](http://rhydon/jenkins/login)
Also All My Old Jobs Are There!

While we are here I did another deploy just to make the agent pods have
4GB of Ram and 4CPU.

I should instead of using the full jenkins-values.yml just changing the fields I changed.

So the kubernetes jenkins plugin doesn't seem to work with a jenkinsPrefixUri....
`SEVERE: http://master-jenkins2.default.svc.cluster.local:8080/tcpSlaveAgentListener/ is invalid: 404 Not Found`
It isn't putting the jenkinsPrefixUri on the url

I found that I can set the JENKINS_URL environment variable in the pod_template configuration area.
This made it connect.

I then replaced the container with
`jenkins/inbound-agent:4.3-4`

Now I'm playing around with pipelines -- seems like the preferred way of dealing with it
I am now able to use python container and docker container to run tests

#!/usr/bin/env groovy

def label = "docker-jenkins-${UUID.randomUUID().toString()}"
```
podTemplate(label: label,
        containers: [
                containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:alpine'),
                containerTemplate(name: 'python', image: 'python', ttyEnabled: true, command: 'cat'),
                containerTemplate(image: 'docker', name: 'docker', command: 'cat', ttyEnabled: true)
            ],
            volumes: [
                hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock'),
            ],
        ) {
    node(label) {
        dir("${env.WORKSPACE}") {
            stage('Checkout') {
                timeout(time: 3, unit: 'MINUTES') {
                    // checkout scm
                }
            }

            stage('Check Python Container') {
                container('python') {
                    sh '''#!/bin/bash
                    python --version'''
                }
            }
            
            stage('Docker Hello World') {
                container('docker') {
                    sh "docker run hello-world"
                }
            }
            
            stage('pakage') {
                echo "Hello World"
            }
        }
    }
}
```
volumes.hostPathVolume mounts the NODE'S docker socket to the docker daemon.
Only works on nodes where docker is installed.

Still not sure why the command is 'cat'.


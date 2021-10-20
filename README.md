# hello-app
######################## MINIKUBE & HELM INSTALLATION #########################

Install Ubuntu 20.04.3 on Oracle VM VirtualBox
Update packages === sudo apt update
Set up repository and installed Docker Engine === https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository
Download and install minikube == https://minikube.sigs.k8s.io/docs/start/
Curl and install kubectl  
sysadmin@ubuntuminikube:~$ minikube version
minikube version: v1.23.2
commit: 0a0ad764652082477c00d51d2475284b5d39ceed

Initialize a cluster with minikube == minikube start
Check minikube status

sysadmin@ubuntuminikube:~$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

Enable ingress addons
sysadmin@ubuntuminikube:~$ minikube addons enable ingress

Curl and install helm == https://helm.sh/docs/intro/install/ 

sysadmin@ubuntuminikube:~$ helm version
version.BuildInfo{Version:"v3.7.1", GitCommit:"1d11fcb5d3f3bf00dbe6fe31b8412839a96b3dc4", GitTreeState:"clean", GoVersion:"go1.16.9"}


######################## HELLO-APP DEPLOYMENT ##############################

Copy manifest files
create directory hello-app and copy manifests files into this directory
Edit the files to deploy and expose the application

sysadmin@ubuntuminikube:~$ mkdir hello-app
sysadmin@ubuntuminikube:~$ cd hello-app
sysadmin@ubuntuminikube:~/hello-app$ vi helloweb-deployment.yaml
sysadmin@ubuntuminikube:~/hello-app$ vi helloweb-hpa.yaml
sysadmin@ubuntuminikube:~/hello-app$ vi helloweb-ingress-static-ip.yaml

Deploy the app, set up autoscaling, and expose it by applying the manifest files 

sysadmin@ubuntuminikube:~$ kubectl apply -f hello-app
deployment.apps/helloweb created
horizontalpodautoscaler.autoscaling/cpu created
ingress.networking.k8s.io/helloweb created
service/helloweb-backend created
sysadmin@ubuntuminikube:~$ kubectl get all
sysadmin@ubuntuminikube:~/hello-app$ kubectl get all
NAME                            READY   STATUS    RESTARTS   AGE
pod/helloweb-67c794d6df-bvrjw   1/1     Running   0          22m

NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/helloweb-backend   NodePort    10.109.51.150   <none>        8080:32395/TCP   8s
service/kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP          94m

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/helloweb   1/1     1            1           22m

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/helloweb-67c794d6df   1         1         1       22m

NAME                                      REFERENCE             TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/cpu   Deployment/helloweb   <unknown>/30%   1         5         1          88m

sysadmin@ubuntuminikube:~/hello-app$ curl http://10.109.51.150:8080
Hello, world!
Version: 2.0.0
Hostname: helloweb-67c794d6df-bvrjw

sysadmin@ubuntuminikube:~/hello-app$ minikube service --url helloweb-backend
http://192.168.49.2:32395
sysadmin@ubuntuminikube:~/hello-app$ curl http://192.168.49.2:32395
Hello, world!
Version: 2.0.0
Hostname: helloweb-67c794d6df-bvrjw


########################### PUSHING HELLO-APP REPOSITORY TO GIT ######################################

sysadmin@ubuntuminikube:~/hello-app$ git init
Initialized empty Git repository in /home/sysadmin/hello-app/.git/
sysadmin@ubuntuminikube:~/hello-app$ git config --global user.email tosyn126@yahoo.com
sysadmin@ubuntuminikube:~/hello-app$  git config --global user.name Oluolapeju
sysadmin@ubuntuminikube:~/hello-app$ git add "/home/sysadmin/hello-app"
sysadmin@ubuntuminikube:~/hello-app$ git commit -m "first commit"
[master (root-commit) 3ab7906] first commit
 3 files changed, 72 insertions(+)
 create mode 100644 helloweb-deployment.yaml
 create mode 100644 helloweb-hpa.yaml
 create mode 100644 helloweb-ingress-static-ip.yaml
sysadmin@ubuntuminikube:~/hello-app$ git branch -M main
sysadmin@ubuntuminikube:~/hello-app$ git remote add master https://github.com/Oluolapeju/hello-app.git
sysadmin@ubuntuminikube:~/hello-app$ git push -u master main
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 6 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 956 bytes | 318.00 KiB/s, done.
Total 5 (delta 0), reused 0 (delta 0)
To https://github.com/Oluolapeju/hello-app.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'master'.
sysadmin@ubuntuminikube:~/hello-app$








# GitOps CI/CD KubernetesChallenge
DigitalOcean Kubernetes Challenge 

# Deploy a GitOps CI/CD implementation

>GitOps is an architecture pattern of using Git repositories as the source of truth for defining the desired application state.
>Argo CD is implemented as a kubernetes controller which continuously monitors running applications and compares the current, live state against the desired target state (as specified in the Git repo).

>In this tutorial we will use Tekton and Kaniko as our CI tooling to build the stickynotes application and push to dockerhub.
>ArgoCD will be our Continuous Deployment and Delievery tooling to deploy the application to the 3 node kubernetes cluster on digitalocean


![ArgoCD - Architecture](images/argocd_architecture.png)

## Requirements

- doctl
- Kubectl
- Tekton and Kaniko
- ArgoCD

## Steps

- To begin with you would have to install all the above tools on your windows, linux or Mac machine.


- Authentication in DigitalOcean

```
doctl auth init [ enter digitalocean token when prompted]
```

- Follow the reference guide below to create a cluster on digitalocean:
  reference: [cluster creation guide](https://docs.digitalocean.com/products/kubernetes/how-to/create-clusters/)

- Follow the reference guide below to connect to a cluster on digitalocean:

  reference: [cluster creation guide](https://docs.digitalocean.com/products/kubernetes/how-to/connect-to-cluster/)

- Follow the reference guide below to install and setup argocd on the cluster:

  reference: [cluster creation guide](https://argo-cd.readthedocs.io/en/stable/getting_started/)

- Argocd 

![ArgoCD - Architecture](images/argocd_architecture.png)

- Apply the manifest files to build and push the image to docker hub

```
1. kubectl apply -f secrets.yaml
2. kubectl apply -f dockerhub-service.yaml
3. kubectl apply -f stickynotes-code.yaml
4. kubectl apply -f registry.yaml
5. kubectl apply -f task.yaml
6. kubectl apply -f taskRuner.yaml
```

- After sometime run the command below to verify if the image has succesfully been pushed to been built and pushed

```
tkn taskrun describe build-docker-image-from-git-source-task-run
```
![Tekton - Status](images/tekton1.png)



```
md -Force "$Env:APPDATA\vcluster"; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]'Tls,Tls11,Tls12';
Invoke-WebRequest -UseBasicParsing ((Invoke-WebRequest -URI "https://github.com/loft-sh/vcluster/releases/latest" -UseBasicParsing).Content -replace "(?ms).*`"([^`"]*vcluster-windows-amd64.exe)`".*","https://github.com/`$1") -o $Env:APPDATA\vcluster\vcluster.exe;
$env:Path += ";" + $Env:APPDATA + "\vcluster";
[Environment]::SetEnvironmentVariable("Path", $env:Path, [System.EnvironmentVariableTarget]::User);
```

- To confirm that vcluster CLI is successfully installed, test via:

```
vcluster --version
```

![Image1](Images/Screenshot_1.png)

- Authentication in DigitalOcean

```
doctl auth init
```

![Image2](Images/Screenshot_2.png)

- Provisioning kubernetes cluster in DigitalOcean

```
doctl kubernetes cluster create do-kuberneteschallenge-2021 --region nyc1
```

![Image3](Images/Screenshot_3.png)

- Verify active nodes

```
kubectl get nodes
```

![Image4](Images/Screenshot_4.png)

- Deploy vcluster

```
vcluster create vcluster-1 -n host-namespace-1
```

![Image6](Images/Screenshot_6.png)

- Running vcluster

```
vcluster connect vcluster-1 --namespace host-namespace-1
```
![Image5](Images/Screenshot_5.png)


- Connect to vcluster (in another terminal)

```
export KUBECONFIG=$PWD/kubeconfig.yaml
kubectl get nodes
```

![Image7](Images/Screenshot_7.png)

- Test the nginx application in this new vcluster

```
kubectl run nginx --image=nginx
kubectl expose pod nginx --port=80
#verify the pod and service
kubectl get pods
kubectl get svc
#Use port forwarding to access nginx application
kubectl port-forward nginx 9080:80
```

![Image8](Images/Screenshot_8.png)

![Image9](Images/Screenshot_9.png)

![Image10](Images/Screenshot_10.png)



- done!


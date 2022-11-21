# Python App AWS Infrastructure

## Pre-requisites

* AWS Account
* IAM User/Role with Administrator Access
* AWSCLI
* Git
* Terraform
* Kubectl
* jq

## Clone Infrastructure Repository

Clone the infrastructure repository by running the following command:

```console
git clone https://github.com/Belyklarry/python-app-aws-infra.git
```

## Deploy Infrastructure

```cd python-app-aws-infra``` into the repo.
Now to initialize terraform, run:

```console
terraform init
```

After initializing the repo with Terraform, run:

```console
terraform plan
```

Finally, run:

```console
terraform apply -auto-approve
```

## Deploy Application

In ```main.tf```, in the ```kubernetes_addons``` module, the workload is commented out. Uncomment it, save the file, and again run:

```console
terraform apply -auto-approve
```

**NB: This deploys the application into the cluster.**

## Access the EKS cluster and Application

After the deployment, a command to access the EKS cluster will be displayed in the outputs. Run that command.
You can now use kubectl to access the cluster.
To access the application on your browser, run:

```console
Kubectl -n argocd get svc
```

* Copy and paste the loadbalancer URL for argocd server.
* Username is ```admin```.
* The password is obtained from:

```console
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Here you can see all the resources created in your cluster. To access the python-app, click on the python-app ingress icon and it'll redirect you to the application homepage.
**NB: Ingress will take about 30 minutes for it to start serving traffic.**

## Cleanup

In order to let Terraform cleanup all the resources that it has created, we first need to delete the resources created outside Terraform like the load balancer and EBS volume. For that, we comment out the line deploying our workloads in the ```main.tf``` file. For the changes to take effect, run:

```console
terraform apply -auto-approve
```

**First remove the ```kubernetes_addons``` module:**

```console
terraform destroy -target=module.kubernetes_addons -auto-approve
```

**We can now safetly delete our EKS cluster:**

```console
terraform destroy -target=module.eks_blueprints -auto-approve
```

**Finally we can delete the VPC and all remaining services:**

```console
terraform destroy -auto-approve
```

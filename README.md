# Basic Local Test Environment

* Provision local development environment in EKS with Terraform
## Pre-requisites
* AWS Account
* IAM User/Role with Administrator Access
* AWSCLI
* Git
* Terraform
* Kubectl
* jq
## AWS Account
If you do not have an AWS account, follow the instructions on this [link](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/) on how to set it up.
[Create](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) a user with Admin Privileges and [configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) your local machine to use those credentials.
## Clone Infrastructure Repository
Clone the infrastructure repository by running the following command:
```console
git clone https://github.com/Belyklarry/clj-app-infra.git
```
## Deploy Infrastructure
```cd clj-app-infra``` into the repo.
Now run: ```terraform init```
After initializing the repo with Terraform, run: ```terraform plan```. This is to see what terraform will deploy on AWS on your behalf.
Finally, run: ```terraform apply```. This will show you the plan again and you’ll have to respond to the prompt with a “yes”.
The deployment will take about 15 minutes or so.
## Deploy Application
In ```main.tf```, in the ```kubernetes_addons``` module, the workload is commented out. Uncomment it, save the file, and again run:
```console
terraform plan
```
Followed by:
```console
terraform apply -auto-approve
```
This deploys the application into the cluster.
## Access the EKS cluster and Application
After the deployment, a command to access the EKS cluster will be displayed in the outputs. Run that command.
You can now use kubectl to check what’s in the cluster.
To access the application on your browser, run:
```console
Kubectl -n clj-microservices get ingress
```
Copy and paste the ingress URL of the clj-microservices app on your browser and the front-end of the application will be displayed.

**NB: For more information on the solution, read the ```solution-documentation.md``` file.**

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
**Finally we can delete the VPC and all remaining services**
```console
terraform destroy -auto-approve
```
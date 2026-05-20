# Super-Mario-Project
### $\color{red} \textbf {Step 1 → Login  and  basics  setup}$
1. Click on launch Instance
  - t2.medium
  - ubuntu
2. Connect to EC2-Instance
3. Attach to IAM Role
 -AdministratorAccess   #only required
 -AmazonEKSClusterPolicy
 -AmazonEKSWorkerNodePolicy
 -AmazonEKS_CNI_Policy
 -AmazonEC2ContainerRegistryFullAccess
 -AmazonEC2FullAccess
 -AmazonVPCFullAccess
 -AutoScalingFullAccess
 -IAMFullAccess
 -CloudWatchFullAccess
### $\color{red} \textbf {Step 2 → Setup  Tools}$

````
sudo apt update -y
````
$\color{blue} \textbf {Setup  Docker:}$
````
sudo apt install docker.io -y
sudo systemctl start docker
sudo usermod -aG docker ubuntu
newgrp docker
docker --version
````
````
sudo yum install docker -y
sudo systemctl start docker
sudo usermod -aG docker ec2-user
newgrp docker
docker --version
````

$\color{blue} \textbf {Setup Terraform:Ubuntu}$
````
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

````
- Amazon linux
````
sudo yum install -y yum-utils shadow-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
sudo yum install terraform -y
````
${\color{blue} \textbf {Setup  AWS CLI:}}$
````
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip 
unzip awscliv2.zip
sudo ./aws/install
aws --version

````

## ${\color{blue} \textbf {Install kubectl}}$
Download the latest release with the command:
````
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
````

Install kubectl:
````
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
````
Note:
If you do not have root access on the target system, you can still install kubectl to the ~/.local/bin directory:
````
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
````
````
kubectl version --client
````
### $\color{red} \textbf {Step 5 → Building Infrastructure  Using  terraform}$
$\color{blue} \textbf {Install  GIT}$
````
git clone https://github.com/abhipraydhoble/Project-Super-Mario.git
````
````
cd Project-Super-Mario
cd EKS-TF
````
````
vim backend.tf
````

$\color{blue} \textbf {Create \ Infra:}$
````
terraform init
````
Check available profiles:
````
aws configure list-profiles
````
If eks is not listed, create it:
````
aws configure --profile eks
````
It will ask for:

AWS Access Key ID
AWS Secret Access Key
Default region name
Default output format
Then verify:
````
aws sts get-caller-identity --profile eks
````
If it returns your AWS account details, run:
````
terraform init
````
again.
Create the bucket manually using AWS CLI:
````
aws s3 mb s3://shriyali-terraform-backend-2004 --region ap-south-1 --profile eks
````
Then verify:
````
aws s3 ls --profile eks
````
You should see:
````
shriyali-terraform-backend-2004
````
After that run:
````
terraform init
````
again.
Edit your main.tf.

Example:
````
resource "aws_iam_role" "example" {
  name = "eks-cluster-cloud-v2"
}
````
````
resource "aws_iam_role" "example1" {
  name = "eks-node-group-cloud-v2"
}
````
Then:
````
terraform apply
````
Import Existing Roles into Terraform State (professional approach)

If those roles are already correct and you want Terraform to manage them:
````
terraform import aws_iam_role.example eks-cluster-cloud
````
````
terraform import aws_iam_role.example1 eks-node-group-cloud
````
Then run:
````
terraform plan
````
````
terraform apply --auto-approve
````
````
aws eks update-kubeconfig --name EKS_CLOUD --region ap-southeast-1 --profile eks
````

### $\color{red} \textbf {Step 6 → Creation  of  deployment  and service  for  EKS}$
change the directory where deployment and service files are stored use the command →
````
cd ..
````
$\color{blue} \textbf {create  the  deployment}$
````
kubectl apply -f deployment.yaml
````
$\color{blue} \textbf {Now create  the service}$
````
kubectl apply -f service.yaml
kubectl get all
kubectl get svc mario-service
````
copy the load balancer ingress and paste it on browser and your game is running

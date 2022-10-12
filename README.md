# react-dotnet-example

In this project, I deployed a react application with .NET web API on AWS EKS Cluster.

Steps

- I configured AWS CLI 

- I dockerized the application using docker by creating a Dockefile with multi-stage builds in order to reduce the image size, I also used Microsoft Dotnet base image from docker hub.

- Built the image

- I created a docker repository in Amazon ECR to store the docker image via AWS CLI

	- First I authenticated to the default registry by setting up my account ID and region
			§ aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin aws_account_id.dkr.ecr.us-east-1.amazonaws.com

	- Then I created a repository
			aws ecr create-repository \     
			     --repository-name frontend/web-app \     
			     --image-scanning-configuration scanOnPush=true \
			     --image-tag-mutability IMMUTABLE \    
			     --region us-east-2
			
- Next I tagged the docker image I have in my local by using the repository URL that I created

- Next was to push the docker image to the repository (AWS ECR)
	
Creating the cluster and worker node

- To create the cluster I created an IAM role (with permission "AmazonEKSClusterPolicy") for the EKS to allow Amazon EKS and K8s to manage AWS resources.
	
- Creating the nodes by adding a node group in the cluster that I created, I also created an IAM role (with permission "AmazonEKSWorkerNodePolicy", "AmazonEC2ContainerRegistryReadOnly", and "AmazonEKS_CNI_Poilcy") and attached it to the node group.
		eksctl create nodegroup \
		  --cluster my-cluster \
		  --region region-code \
		  --name my-mng \
		  --node-type m5.large \
		  --nodes 3 \
		  --nodes-min 2 \
		  --nodes-max 4 \
		  --ssh-access \
		  --ssh-public-key my-key
		
	
Configure kubectl to use the cluster

- I installed aws-iam-authenticator in order to authenticate the cluster

- Then used AWS CLI update-kubeconfig command to update kubeconfig for the cluster

Deploying K8s objects on AWS EKS cluster

- I created a deployment and service object file by using the image from AWS ECR

- configured kubectl to communicate with the cluster, deploy, and manage the application on the cluster

- used kubectl to create the deployment and service via the yaml file.


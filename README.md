# Creating the EKS cluster and deploying mongodb and mongo express

This repository contains the steps involved for

- Creating EKS cluster
- Installing the AWS EBS CSI driver for the EKS cluster
- Deploying the applications in the EKS cluster
- Testing the application

## Pre-requisites

- Create the AWS key pair for logging into the worker nodes and note down the key pair name which will be needed in later stages

- Install the [awscliv2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) depending on your OS

- Install the [eksctl](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html) depending on your OS

- Install the [kubectl](https://kubernetes.io/docs/tasks/tools/) depending on your OS

## Creating the EKS cluster

- Create a user within AWS account and give necessary permissions to create the EKS cluster.

- Configure the access keys and secret keys for the user using the command `aws configure`

- Once the required binaries are installed on your system run the command inside the **Kubernetes -> Cluster-setup** folder. This will create a EKS cluster in your AWS account.

```
You can create the cluster on any of the AWS region that supports EKS. I have used eu-west-2 in this demo.
Wait for 15-20 mins for the cluster to be completely active
```

## Installing the AWS EBS CSI driver for the EKS cluster

- This step is needed for the storage of our application running inside the cluster to be persistent withing the AWS native EBS storage.

- Run the steps provided in this link [AWS EBS CSI Driver](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)

## Deploying the applications in the EKS cluster

- Create a kubernetes namespace object named `mongo-db-ns` to have all our deployments related to mongodb to be present within this namespace for isolation

```
kubectl create ns mongo-db-ns
```

- Create a kubernetes secret object named `mongo-db-secret` inside the `mongo-db-ns` namespace to store the mongodb initial root username and password

```
kubectl create secret generic mongo-db-secret --from-literal=mongo-db-root-username=<your-username> \
--from-literal=mongo-db-root-password=<your-password> -n mongo-db-ns
```

- Finally run the deployment manifest files present inside the folder **Kubernetes -> Kubernetes-manifests** in the below following order

```
mongo-db-sc.yaml
mongo-db-pvc.yaml
mongo-db.yaml
mongo-db-service.yaml
mongo-db-configmap.yaml
mongo-express.yaml
mongo-express-service.yaml
```

## Testing the application

- Login to any one of the worker nodes and hit the below command

```
curl -v http://<public-dns-of-the-instance>:<node-port-number-configured-in-mongo-express-service.yaml-file>
```
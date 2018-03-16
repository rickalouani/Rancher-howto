Four ways to build Kubernetes clusters with Rancher 2.0

Deploying a production grade Kubernetes cluster is by no means an 
easy task. A Kubernetes cluster has many moving parts, and no standard way to deploy it across platforms. Depending on your use case you need to select the right tool for your deployment. Tools include KOPS, KUBEADM, conjure-up, minikube, RKE etc. 
In this article, I am going to show the power of Rancher 2.0 and use it to deploy a kubernetes cluster 
4 different ways:
   1. GKE (Google Kubernetes Engine) and importing the cluster to Rancher 2.0
   2. RKE (Rancher Kubernetes Engine) on Google Cloud Platform
   3. On AWS
   4. Custom cluster using VMs

Along the way, I will highlight some of the pros and cons of each method of deployment.

* assumption: Google Cloud Platform, and, or AWS account is already setup.  < I will add links to how to docs>

* assumption: User has already configured Rancher 2.0

Create a cluster using RKE (Rancher Kubernetes Engine)

1. 
Log in to Rancher 2.0 server
Click Add Cluster  
Click Select in the Launch a Cloud Cluster Tab
Give the cluster a name
Click
Click CREATE SERVICE ACCOUNT  


    

Pros: all of the Rancher AD/policy stuff works.  Workload experience works

Cons: have to use Google's current version of k8s. NOt much confi options with kubernetes. 

Create a local cluster using RKE

pros: Runs anywhere

Pros: easy to create a resiliant cluster (3 etcd nodes, 2 managers, 1+ workers)

Pros: easy upgrade. 

Create a cluster in Digital Ocean/Packet/AWS 

using the Node Drivers in Rancher (which are based on Docker Machine)

Pros - runs on any cloud - easy to automate

Pros - rancher automates the upgrade of the cluster

Cons - not as tightly integrated with the cloud provider as GKE/EKS/Azure Kubernetes. 

Import a cluster that you create outside of Rancher

Pros - visibility into any cluster, even if it already exists

Cons - Rancher doesn't have the ability to expand the number of nodes.

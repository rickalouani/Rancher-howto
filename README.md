**Four ways to build Kubernetes clusters with Rancher 2.0**

Deploying a production grade Kubernetes cluster is by no means an easy task.
A Kubernetes cluster has many moving parts, and no standard way to deploy it
across cloud platforms. If your use-case calls for multiple deployments across
cloud providers the complexity increases tremendously. The deployment
tools available such as KOPS, KUBEADM, conjure-up, minikube, etc, are all
cloud-provider-gnostic and cli driven. So, to deploy and manage multiple
Kubernetes clusters requires system administrator skills and the clusters
have to be managed independently.

In this article, I am going to show the power of Rancher 2.0 and use it to deploy a kubernetes cluster
4 different ways:
   1. RKE (Rancher Kubernetes Engine) on Google Cloud Platform
   2. GKE (Google Kubernetes Engine) and importing the cluster to Rancher 2.0
   3. On AWS
   4. Custom cluster using VMs

Along the way, I will highlight some of the pros and cons of each method of deployment.

* assumption: Google Cloud Platform, and, or AWS account is already setup.  < I will add links to how to docs>

* assumption: User has already configured Rancher 2.0

**Create a cluster using RKE (Rancher Kubernetes Engine)**

1. Log in to Rancher 2.0 server

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-1.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-2.png)


2. Click **Add Cluster**


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-5.png)


3. Click **Select** in the Launch a Cloud Cluster Tab


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-4.png)

At this point, you need to log on to you Google Cloud account and create a SERVICE ACCOUNT. Please note in the above
screenshot, the SERVICE ACCOUNT has to be created with **project/viewer**, **kubernetes-engine/admin**, and **service-account/user** IAM roles:
We need the service account credentials to be able to access the Google Cloud account and deploy our kubernetes cluster.
The credentials will be provided by Google in a JSON format after the service account is created. We can paste the JSON content into Service Account in the screenshot above, please see step
      
      

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-1.png)


5. On the upper left of the page **Click Product and Services** and **select
   IAM & admin -> Service accounts**



![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-2.png)

7. **Click CREATE A SERVICE**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-3.png)

8. Give the Service Account a name


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-4.png)

9. On the select a role tab select **project/viewer**, **kubernetes-engine/admin**, 
   and **service-account/user** IAM roles

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-5.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-6.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-7.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-8.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-9.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-10.png)

10. **Click on the three dots to the right of the new service account name** as seen in 
    the above screenshot and select **Create key**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-11.png)

11. **Click Create**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-12.png)

   A Json file will be saved to your local machine as seen above



13. Copy and Paste or upload the JSON file as seen in the following screenshot and **click Next**
  
![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-3.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-8.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-9.png)



![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-10.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-11.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-12.png)
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

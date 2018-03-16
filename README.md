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
   1. GKE (Google Kubernetes Engine) and importing the cluster to Rancher 2.0
   2. RKE (Rancher Kubernetes Engine) on Google Cloud Platform
   3. On AWS
   4. Custom cluster using VMs

Along the way, I will highlight some of the pros and cons of each method of deployment.

* assumption: Google Cloud Platform, and, or AWS account is already setup.  < I will add links to how to docs>

* assumption: User has already configured Rancher 2.0

**Create a cluster using RKE (Rancher Kubernetes Engine)**

1. Log in to Rancher 2.0 server

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-1.png)

2. Click **Add Cluster**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-2.png)

3. Click **Select** in the Launch a Cloud Cluster Tab

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-4.png)
4. Give the cluster a name

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-3.png)

5. Step 5
![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-5.png)

6. Create service account

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-1.png)

7. Step 7

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-2.png)

8. Step 8

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-3.png)

9. Step 9

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-4.png)

10. Step 10

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-5.png)

11. Step 11

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-6.png)

12. Step 12

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-7.png)

13. Step 13

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-8.png)

14. Step 14

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-9.png)

15. Step 15

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-10.png)

16. Step 16

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-11.png)

17. Step 17

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-sa-12.png)
18. Step 18

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster-7.png)





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

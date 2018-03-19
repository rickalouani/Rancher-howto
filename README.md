**** This is a work in progress ****

**Four ways to build Kubernetes clusters with Rancher 2.0**

A Kubernetes cluster has many moving parts, and no standard way to deploy it 
across cloud platforms. The tools avaialbe for bootstraping a Kubernetes cluster 
such as  KOPS, KUBEADM, conjure-up, and MINIKUBE are great tools for standing up 
a cluster, and do make the process very easy, however, they are all cloud-provider-gnostic 
and CLI based. So, to deploy and manage multiple Kubernetes clusters requires system 
administrator skills. Furthermore, If your use-case calls for multiple deployments across 
different cloud providers the complexity increases tremendously. Some of the challenges 
include, Each deployment would have to be version-controlled in a seperate branch, all 
updates, upgrades, rolebacks, and patches would have to be deployed separately for each 
cluster, it could get overwhelming pretty quick. 

Rancher 2.0 is an opensource UI based container orchestration platform that includes its own 
Kuberentes Engine RKE, and allows the user to create, manage, and monitor multiple kubernetes 
clusters across different cloud providers. It also allows a user to import the management of 
existing Kubernetes clusters as I will demonstrate later in the document.

In the following pages I am going to demonstrate the power and friendliness of Rancher 2.0. 
I will use it to deploy a kubernetes cluster 4 different ways:

   1.  RKE (Rancher Kubernetes Engine) on Google Cloud Platform
   2.  GKE (Google Kubernetes Engine) and importing the cluster to Rancher 2.0
   3.  On DigitalOcean
   4.  Custom cluster using VMs

Along the way, I will highlight some of the pros and cons of each method of deployment.

*   assumption: Google Cloud Platform, and, or AWS account is already setup. < I will add links to how-to-docs>

*   assumption: User has already configured Rancher 2.0 < will add links to how-to-docs>

**Create a cluster using RKE (Rancher Kubernetes Engine)** 

Please note that RKE is not enabled by default on Google Cloud Platform. Please see 
Enabling RKE on Google Cloud Platform section at the end of the document.


  
  

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

**GKE (Google Kubernetes Engine) and importing the cluster to Rancher 2.0***


1. Step 1
![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE1.png)

2. Step 2

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE2.png)

3. Step 3

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE3.png)

4. Step 4

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE4.png)


5. Step 5

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE5.png)


6. Step 6

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE6.png)


7. Step 7


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE7.png)


8. Step 8

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE8.png)


**Enabling RKE on Google Cloud Platform**

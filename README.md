
A Kubernetes cluster is a collection of resources (hosts, storage, networking)
and technologies(docker, SDN, CNI, RESTful API design) integrated together
intelligently to provide a potent container and batch job orchestration
platform. Kubernetes' design is beautifully simple, a well design API, an api
a reliable data, a scheduler


Kubernetes emboddies the concept of the DATA CENTER as a computer.
Once provisioned, Kubernetes will abstract away the complexity of managing
multiple hosts and present a multinode cluster as a single entity. If Node
affinity is not a requirement, a user would launch workloadsand kubernetes
would schedule them somewhere in the cluster.
The tools avaialbe for bootstraping a Kubernetes cluster such as KOPS,
KUBEADM, conjure-up, and MINIKUBE are great tools for standing up
a cluster, and do make the process very easy. However, they are all cloud
platform gnostic and CLI based, furthermore, as long as AWS,y GOOGLE, AZURE,
ALIBABA, and the rest of the cloud providers insist on using proprietery
APIs for obvious reasons, the situation is not going to change anytime soon.
So, to deploy and manage multiple Kubernetes clusters requires system
administrator skills. Furthermore, If your use-case calls for multiple
deployments across different cloud providers the complexity increases
tremendously. Some of the challenges include, Each deployment would have to be
version-controlled in a seperate branch, all updates, upgrades, rolebacks,
and patches would have to be deployed separately for each
cluster, it could get overwhelming pretty quick.

Rancher 2.0 is an opensource UI based container orchestration platform that includes
its own Kuberentes Engine RKE(Rancher Kubernetes Engine), and allows the user
to create, manage, and monitor multiple kubernetes clusters across different cloud
providers from a single UI. It also allows a user to import existing Kubernetes clusters
as I will demonstrate later in the article.

In the following pages I am going to demonstrate the power and friendliness of Rancher 2.0.
I will use it to deploy a kubernetes cluster 4 different ways:

   1.  RKE to build  on Google Cloud Platform  from scratch
   2.  GKE (Google Kubernetes Engine) and importing the cluster to Rancher 2.0
   3.  Create a Kubernetes cluster on DigitalOcean  from scratch
   4.  Custom cluster using VMs

Along the way, I will highlight some of the pros and cons of each method of deployment.

*   assumption: Google Cloud Platform, and, or AWS account is already setup. < I will add links to how-to-docs>

*   assumption: User has already configured Rancher 2.0 < will add links to how-to-docs>



Pros:

Abstracts away and manages Kubernetes master nodes
Nodes use container optimized image
Upgrade Kubernetes Master through GCP management console
Upgrade, add, or remove worker nodes through GCP management console
Global spanning load balancer built-in
Easy deployment and automation
Optional support for Kops
Streamlined integration with Codefresh
Cons:

Unable to login to Kubernetes master nodes and change advanced settings*

*This con is a little unfair Kops supports Google Cloud which means you can always go beyond the managed service to build your own master nodes.

Amazon Web Services (AWS)
AWS is the most mature public cloud, and many businesses already have a well established presence on AWS. AWS has their own proprietary container orchestrator, Elastic Container Service (ECS), but it is different than Kubernetes.

The project Kubernetes Operations (kops) has become the de-facto standard for creating, upgrading, and managing Kubernetes clusters on AWS. Kops is a well maintained open source project with an active community.

Pros: (kops)

Deploy HA Kubernetes master nodes
Upgrade Kubernetes master nodes
Upgrade, add, or remove worker nodes
kops is highly configurable
Supported by Codefresh
Cons:

No support for Kubernetes in AWS management console; must use kops or another third-party service
kops configurations need to be managed and backed up


Kubernetes in the Cloud: AWS vs. GCP vs. Azure
Kubernetes Tutorial | July 21, 2017
*** Limited time offer: Create a free Codefresh Account and get $500 GCP Credit ***

Kubernetes is a powerful container orchestrator that allows automating, scaling, and managing container deployments. We have already covered Kubernetes basics in the past, check out that post if you want to learn more.

In this comparison we’ll look at how easy Kubernetes is to setup and manage as well as the costs and features in the big cloud. Plus we’ll look at how easy they are to integrate into your Kubernetes deployment flow with Codefresh.

Amazon Web Services (AWS), Google Cloud Platform (GCP), and Microsoft Azure all have services that are capable of setting up Highly Available installations of Kubernetes. Let’s take a look at the pros and cons of each cloud.


Amazon Web Services (AWS)
AWS is the most mature public cloud, and many businesses already have a well established presence on AWS. AWS has their own proprietary container orchestrator, Elastic Container Service (ECS), but it is different than Kubernetes.

The project Kubernetes Operations (kops) has become the de-facto standard for creating, upgrading, and managing Kubernetes clusters on AWS. Kops is a well maintained open source project with an active community.

Pros: (kops)

Deploy HA Kubernetes master nodes
Upgrade Kubernetes master nodes
Upgrade, add, or remove worker nodes
kops is highly configurable
Supported by Codefresh
Cons:

No support for Kubernetes in AWS management console; must use kops or another third-party service
kops configurations need to be managed and backed up
Google Cloud Platform (GCP)
Google Container Engine (GKE) is Google Cloud Platform’s managed Kubernetes service. Google is the #1 contributor to the Kubernetes open source project and GKE has been around since the early (pre 1.0) days of Kubernetes.

Google manages the Kubernetes master nodes for you in GKE, meaning that you don’t have access to them but you also aren’t charged for their compute resources. If your GKE cluster grows above 5 nodes, it costs around $100 per month (in addition to the worker node compute resources), which is on-par with what it would cost to run your own master nodes.

Another feature that sets GCP apart is that they provide a global spanning load balancer built-in which is autoconfigured when services are created. This load balancer is essentially the same that handles Youtube.com and Google.com. On Amazon and Azure the load balancer is just another container instance which you need to scale.

Pros:

Abstracts away and manages Kubernetes master nodes
Nodes use container optimized image
Upgrade Kubernetes Master through GCP management console
Upgrade, add, or remove worker nodes through GCP management console
Global spanning load balancer built-in
Easy deployment and automation
Optional support for Kops
Streamlined integration with Codefresh
Cons:

Unable to login to Kubernetes master nodes and change advanced settings*
*This con is a little unfair Kops supports Google Cloud which means you can always go beyond the managed service to build your own master nodes.

Microsoft Azure
Azure Container Service (ACS) allows for quick deployment of Kubernetes to Microsoft Azure. ACS is the newest tool of the three clouds to support Kubernetes.

ACS acts as more of a deployment template since it does not include features to upgrade a cluster after it has been deployed. In order to upgrade clusters using ACS, you would need to use ACS to create a new cluster, migrate containers to it, and remove the old cluster.

One bonus feature of Azure is that creating templates out of your infrastructure is very easy. Once you create and configure all your services you can essentially take a snapshot and deploy it again. Those familiar with Amazon’s Cloud Formation Templates will find Azure’s system much easier to use.

Pros:

Deploy HA Kubernetes master nodes
Add or remove worker nodes through Azure management console
Supported by Codefresh
Save infrastructure definition
Cons:

Upgrading Kubernetes nodes is a manual process


Kubernetes on a budget
Of course, you can run Kubernetes with a lot less hardware but it’s hard to configure a fair comparison for a lower price because Google Cloud just provides so much more RAM by default. The smallest option on Google Cloud has 3.75GBs of RAM and costs around $11/mo. Below 5 nodes Google Cloud waives fees related to cluster management which on other platforms would be part of your normal compute pricing.


Cost Comparison
Kubernetes Hosting Cost Comparison (Monthly)
Amazon Web Services	Google Cloud Platform	Microsoft Azure
20 x m4.xlarge nodes
(80 CPUs, 320GB RAM)	20 x n1-standard-4 nodes
(80 CPUs, 320GB RAM)	20x D2-64-v3 nodes
(80 CPUs, 320GB RAM)
$2,880/mo	$1,648.50/mo	$2,880/mo

in GKE to enable helm

kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'



Pros and Cons of the diffent deployments:

There are far more cloud options than AWS, GCP, and Azure. Kubernetes should be
able to work on any cloud that provides multiple virtual machines and networking
between them.





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

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE2.png)

2. Step 2

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE3.png)

3. Step 3


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE5.png)


4. Step 4

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE6.png)


5. Step 5


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/GKE7.png)


6. Step 6

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/gke10.png)



7. Step 7

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/gke12.png)

8. Step 8

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/gke13.png)

9. Step 9

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/gke14.png)

10. Step 10

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/gke15.png)
11. Step 11

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/gke16.png)
**Enabling RKE on Google Cloud Platform**

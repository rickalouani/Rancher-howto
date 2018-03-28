**** This is a work in progress ****

**Four ways to build Kubernetes clusters with Rancher 2.0**

Making the leap to microservices is not as daunting as it was even
10 years ago. The abundance of information and case studies shared by many 
companies that went through the trials and tribulations helped establish 
solid industry best practices, coupled with many new great open-source tools 
donated or developed and supported by a great community of contributors took a 
lot of the mystery out of the process. 
In this article, I am going to demonstrate two great opensource tools that I feel 
confident can make your adoption of micro-services less challenging. Kubernetes, 
arguably the best open source container orchestrator available and Rancher 2.0 to 
bootstrap and manage custom Kubernetes clusters. 


A Kubernetes Cluster is a great habitat for micro-services. The platform 
provides a wealth of built in functionality and solutions, see table below,
and is backed by wonderful opensource community.  
  
![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100000.png)

At a basic level a Kubernetes cluster is a collection of resources such as hosts, CPU cores,
storage, and Memory and technologies such as Containerization, SDN/CNI, and RESTful API design
all integrated together to give an abstraction layer one level above the container 
run time. With this abstraction, a kubernetes cluster could be viewed as a number of 
container run time connected together using an Overlay Network and presented to 
PODs as a single run time. A Pod is the smallest unit of scheduling in Kubernetes, and
could be viewed as a packaged container(s) and a thin wrapper to allow Kubernetes to manage 
PODs not Containers.
Kubernetes emboddies the concept of the DATA CENTER as a computer. Once provisioned, 
Kubernetes will abstract away the complexity of managing multiple hosts and present a 
multinode cluster as a single entity. A user, or an SA(service account) would POST a 
workload definition in the form of a yaml manifest(s) to the API server. The API server 
would store the manifest in its data store(ETCD) as is. A Kubernetes control loop constantly
comparing current state and desired state would notice the new desired state and engages 
scheduled on that node.watch loop constantly compares the current state and the desired 
state(stored in ETCD)and if they vary kubelet(kubernetes agent on the nodes) will inform  
the API server. The API server will engage the appropriate controller to bring the current 
state in line with the desired state.

 - Below is a comprehensive list of features that Kubernetes provides out of the box:

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/microservice1.png)

In this article I am going to demonstrate Both tools by deploying a Kubernetes cluster 
four different ways:

**1. RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean**

**2.  Import the management of an existing GKE(Google Kubernetes Engine) to Rancher 2.0**

**3.  RKE on AWS**  

**4.  Custom cluster using VMs**


 
The tools avaialbe for bootstraping a Kubernetes cluster such as KOPS, 
KUBEADM, conjure-up, kube-spray, and MINIKUBE are great tools for standing up 
a cluster, and do make the process very easy. However, they are CLI based  
and do require Linux and configuration management skills. 

Let's look at a hypothetical deployment of a Kubernetes cluster on AWS using KOPS:
A minimum list of requirement would be inlcude:
understanding of AWS fundamentals 
  - setup AIM accounts with the proper permissions
  - Setup Route53
  - Setup an S3 bucket to host the cluster configuration  
Ability to install and or configure the correct binaries 
for your Desktop OS:
  - aws cli
  - KOPS
  - kubectl
  - ssh keys
  - Configuration management to keep track of different
    versions of your cluster including KOPS
  - Be able to Roleback a bad upgrade
That's not too bad if you are building a single test Cluster, but if you're
for instance building a production, staging, and test Clusters and for simplicity all three
on AWS. Now you would have 3 different repositories, upgrades and patches have to be scheduled
separately. 
Rancher 2.0 is a Kubernetes management platform. As I will demonstrate, will do all the heavy
lifting required to deploy, customize, import and manage Kubernetes clusters. Rancher 2.0 is 
cloud provider agnostic and can manage and deploy clusters on any supported cloud provider all
from a single UI or CLI. As I will demonstrate later,  scalling up or down, upgrades, rolebacks, 
healthchecks, and more all presented as selectable options in the UI.

Before I start the demo, I make a few point incase you're in the process of selecting
a cloud provider to host your Kubernetes cluster(s). Kubernetes works with all current 
cloud providers thanks to the CLOUD PROVIDER INTERFACE, a collection of well defined data 
types and interfaces as shown in following code snippet. All cloud provider have to do 
to host Kubernetes clusters is to implement the interfaces and datatypes.

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100001.png)

Luckily all major Cloud provider have implemented the interface, so you're pretty much free
to select the provider that suits your use case. Through the Cloud Provider Interface data types and methods
Kubernetes will be able to communicate with the underlying infrastructure provider and allocate all the necessary 
resources (load ballencers, volumes, etc) defined in you yaml manifests. Outside that interface, the cloud
provider has no controll on the cluster, Administration such as upgrades, update, and scalling falls you.
If you need more services, the only option right now is GCP. GCP is the only cloud provider that supports
Kubernetes out of the box. It uses GKE a container management management solution built on Kubernetes to manage 
your clusters. So you don't have to install Kubernetes on GCP, Kubernetes is a built-in part of GCP and will always be
well integrated and well tested. Rancher 2.0 provides it's own Kubernetes Engine RKE that is also supported
on GCP and provides and provides similar features as GKE in managing Kubernetes clusters but
on any support Cloud, not just GCP. So with RKE as your deployment tool you are free to choose 
any provider supported, that is powerfull stuff!


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100005.png)



**RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean**

 The goal of this demonstration is to build a Kubernetes cluster with 1 Master(control node), 
 1 etcd(to store cluster and workload configuration) and  3 worker nodes(to run worloads). 
 The basic architecture is bellow
 
 
![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/1000_1.png)

Before I start the demo, I want to point out a few important issue to understand about 
kubernetes and Public clouds. In a great world, 

From the DigitalOcean Dashboard create a VM to host the Rancher 2.0 server as illustrated below:


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/first1.png)


2.

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-7.png)

3.

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/createssh2.png)


**4. On a terminal on your desktop create a new ssh key pair** 


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-5.png)

**5. Copy and paste your Public key**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/sshkeygen3.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-9.png)


Verify that your newly provisioned VM is ssh accessible.
I will use the ssh private key we created in step  4
 **ssh -i ~/.ssh/id_rsa root@<IP_ADDRESS_OF_VM_1>**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11110.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11111.png)

Now let's update the distribution, repositories and install Docker

 **Note:** I am going to use docker community edition but you are free to use any of the versions supported by Rancher 2.0)
supported docker versions: http://rancher.com/docs/rancher/v1.6/en/hosts/#supported-docker-versions
  

  **# sudo apt update -y**
  **# sudo apt dist-upgrade -y**
  **# sudo apt-get remove docker docker-engine docker.io**

  **# sudo apt-get install \**
  **apt-transport-https \**
  **ca-certificates \**
  **curl \**
  **software-properties-common**

 - Add Docker’s official GPG key

# curl -fsSL \
https://download.docker.com/linux/ubuntu/gpg | \
sudo apt-key add -

- Update the repository
**# sudo add-apt-repository \**
**"deb [arch=amd64] https://download.docker.com/linux/ubuntu \**
**$(lsb_release -cs) \**
**stable"**

**# sudo apt-get update**
- Install docker-ce
**# sudo apt-get install docker-ce -y**

- Make sure that docker is running
**# systemctl status docker**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/dockerstatus.png)
- Run the Rancher server container as follows
**# sudo docker run -d --restart=unless-stopped -p 80:80 -p 443:443 rancher/server:preview**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/serverrunning.png)
 


**point you browser to the ip address of your Rancher 2.0 server**
**Accept the certificate and enter the default user name and password (admin admin)**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/30001.png)

**Click Create Cluster**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/30002.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launchcluster1.png)

We need a Node Template a token in this case from DigitalOcean 

**On digital Ocean Dashboard generate an access token**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/generatetoken.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nametoken.png)

**Copy and paste the token**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/tokenlast.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch2.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch3.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch4.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch5.png)



![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-nodes.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-nodes2.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/clusterkubectl.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500002.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500003.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/50000.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500001.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500004.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/50005.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500006.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500007.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500008.png)



![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60000.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60001.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60002.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60003.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60004.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60005.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60006.png)

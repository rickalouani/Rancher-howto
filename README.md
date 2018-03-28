**** This is a work in progress ****

**Four ways to build Kubernetes clusters with Rancher 2.0**

Making the leap to microservices is not as daunting as it was even 10 years ago. The abundance of 
information and case studies shared by many companies that went through the trials and tribulations 
helped establish solid industry best practices, coupled with many new great open-source tools donated 
or developed and supported by a great community of contributors took a lot of the mystery out of the 
process. In this article, I am going to demonstrate two great opensource tools that I feel confident 
can make your adoption of micro-services less challenging. Kubernetes, arguably the best open source 
container orchestrator available and Rancher 2.0 to bootstrap and manage custom Kubernetes clusters. 
A Kubernetes Cluster is a great habitat for micro-services. The platform provides a wealth of built 
in functionality and solutions, see table below, and is backed by wonderful opensource community.
 - Below is a comprehensive list of features that Kubernetes provides out 
   of the box to help with micro-services adoption
   
   
   

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/microservice1.png)




 - Kubernetes Master and Cluster Components
 
  
![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubearch2.png)




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
comparing current state and desired state would notice the new definition and would engage the
scheduler and the different controllers to bring the current state inline with the desired state.
The desired state is reached when the number of running pods matches the number of replicas 
in the case of a ReplicaSets. That is everything requested in the yaml manifest including all PVCs if any 
are fullfilled, requested Memory, requested CPU cycles, IP addresses are assigned, DNS entries were created, 
endpoints if labels are matched with service labels, and Healthchecks if defined in your Pod template 
all pass. This mode is also refered to as a declarative model. 
 

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubearchi2.png)



In this article I am going to demonstrate Both tools by deploying a Kubernetes cluster 
four different ways:


   **1.  RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean**
   
   **2.  Import the management of an existing GKE(Google Kubernetes Engine) to Rancher 2.0**
   
   **3.  RKE on AWS** 
   
   **4.  Custom cluster using VMs**


 
The tools avaialbe for bootstraping a Kubernetes cluster such as KOPS, 
KUBEADM, conjure-up, kube-spray, and MINIKUBE are great tools for standing up 
a cluster, and do make the process very easy. However, they are CLI based  
and do require Linux and configuration management skills. 

Let's look at a hypothetical deployment of a Kubernetes cluster on AWS using KOPS:
A minimum list of requirement would inlcude:

Understanding of AWS fundamentals 
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
on GCP and provides and provides similar features as GKE in managing Kubernetes clusters but on any supported 
Cloud, not just GCP. So with RKE as your deployment tool you are free to choose any provider supported, 
I don't know about you but that's powerfull stuff. Basically, Rancher 2.0 will allow you to develop your 
application and design your kubernetes clusters VENDOR LOCK IN free. Also, Rancher 2.0 would handle for you 
some of the tricky configurations such as ABAC vs RBAC, User space vs IPTABLES. For instance, Kubernetes 
uses RBAC as the defaul access mode and KUBE-PROXY uses IPTABLES as its default mode for PODs to communicate
accress nodes(see figure below). Unfortunately  you are using KOPS, KUBEADM or any of the other 
CLI based tool you would have to keep track of all the changes both by managing the repositories and branches, and
keeping up with release notes. This applies to your deployments as well as manifests. I just running helm on
1.9 and couldn't because of the change to RBAC. I had to create and install the roles and rolebindings to allow
helm to access kube-system namespace. The moral of the storie is, you need to focus most of your efforts developing your
application and not where and how to deploy it.
 if you run into an issue where helm complains about a release not found or something similar enable RBAC for helm as follows:
 
    # kubectl create serviceaccount --namespace kube-system tiller
    # kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin \
      --serviceaccount=kube-system:tiller
    # kubectl patch deploy --namespace kube-system tiller-deploy -p \
      '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'




  
 ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/700001.png)
 
 
 
 

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100005.png)




**RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean**

 The goal of this demonstration is to build a Kubernetes cluster with 1 Master(control node), 
 1 etcd(to store cluster and workload configuration) and  3 worker nodes(to run worloads). 
 The basic architecture is bellow
 
 
![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/1000_1.png)



     From the DigitalOcean Dashboard create a VM to host the Rancher 2.0 
     server as illustrated below:



![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/first1.png)



![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-7.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/createssh2.png)


    On a terminal on your desktop create a new ssh key pair


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-5.png)

**5. Copy and paste your Public key**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/sshkeygen3.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-9.png)


Verify that your newly provisioned VM is ssh accessible.
I will use the ssh private key we created in step  4 

     # ssh -i ~/.ssh/id_rsa root@<IP_ADDRESS_OF_VM_1>

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11110.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11111.png)



    Note: I am going to use docker community edition but you are free to use any of the versions supported by Rancher 2.0)
    supported docker versions: http://rancher.com/docs/rancher/v1.6/en/hosts/#supported-docker-versions
  
Now let's update the distribution, repositories and install Docker

    # sudo apt update -y
    # sudo apt dist-upgrade -y
    # sudo apt-get remove docker docker-engine docker.io
    # sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
 
Add Docker’s official GPG key

    # curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
Update the repository

     # sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
     # sudo apt-get update
     
Install docker-ce

    # sudo apt-get install docker-ce -y
    
Make sure that docker is running

    # systemctl status docker

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/dockerstatus.png)

Run the Rancher server container as follows

    # sudo docker run -d --restart=unless-stopped -p 80:80 -p 443:443 rancher/server:v2.0.0-alpha22

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/serverrunning.png)
 


    point you browser to the ip address of your Rancher 2.0 server
    Accept the certificate and enter the default user name and password (admin admin)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/30001.png)


    Click Create Cluster 
    

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/30002.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launchcluster1.png)


We need a Node Template a token in this case from DigitalOcean 

    On digital Ocean Dashboard generate an access token


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/generatetoken.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nametoken.png)


    Copy and paste the token
    

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





 **2.  Import the management of an existing GKE(Google Kubernetes Engine) to Rancher 2.0**
 
 In this demo, I will migrate a cluster with live workloads. The am going through the process
 of setting up a Kubernetes cluster on GCP using GKE. Just to make the demo a little interesting
 I install a stable/wordpress HELM(another great tool for installing micro-services, think of it
 as APT or YUM for Kubernetes). Use kubectl to confirm that the pods are running, lookup the
 services, and find the ip address of the Loadbalancer assigned by GKE upon request from 
 Kubernetes on your behal. I will point a browser to the loadbalancer address and confirm that 
 Wordpress is up and running.
 
 Next I will migrate the Cluster to Rancher 2.0, Confirm that all the workloads are still 
 running. Show of some of the awesomeness of Rancher 2.0, scalling up, deleting, ... you get 
 the point. 
 The screenshots are self explanatory please enjoy.
 
 

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6001.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6002.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6003.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6004.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6005.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6006.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6007.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6008.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6009.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6010.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6011.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6012.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6013.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6014.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6015.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6016.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6017.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6018.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6019.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6020.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6021.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6022.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6023.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6024.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6025.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6026png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6027.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6028.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6029.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6030.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6031.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6032.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/6033.png)























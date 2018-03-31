
<h2>2 ways to build Kubernetes clusters with Rancher 2.0</h2>

<p> In this article I an going to demonstrate the capabilities of Rancher 2.0 to bootstrap and manage Kuberenetes 
clusters. It abstracts away all the major cloud providers and allows users to deploy Kubernetes clusters accross cloud providers homogeneously insuring the user that worlads would be deployed using the same manifests accross Kubernetes 
clusters regardless of the underlying cloud platform. And argubaly Rancher 2.0's major strength, it's ability to create
Highly available clusters accross cloud providers. 
Standing up a cluster for testing or demos is very easy. But when standing up a production cluster the situation gets complicated. An organization would have to decide on some minimum metrics such as down time, latency, and cost when 
designing their highly available production cluster(s) accordingly. They would need to account of the Number of ETCD 
nodes, API Server instances and the proper ingresses to loadbalancing between them, etc. Also, The ability to update 
clusters on demand.
In the most simplistic terms, Kubernetes will do its thing once up and running, it will make your workload fault tolerant
and highly available, the problem is 1 level above kubernetes. To manage multiple clusters or to have redundant clusters
would required another layer of control on top of Kubernetes. Kuberetes could be used to control Kubernetes clusters(cluster federation) but very complicated. And it's not a coincidense that Rancher 2.0 provides the ability to control multiple Kubernetes clusters out of the Box accross, is was built on Kubernetes.

<p> At a basic level a Kubernetes cluster is a collection of resources such as hosts, CPU cores, storage, and Memory and technologies such as Containerization, SDN/CNI, and RESTful API design all integrated together to give an abstraction layer one level above the container run time. With this abstraction, a kubernetes cluster could be viewed as a number of container run time connected together using an Overlay Network and presented to PODs as a single run time. A Pod is the smallest unit of scheduling in Kubernetes, and could be viewed as a packaged container(s) and a thin wrapper to allow Kubernetes to manage PODs not Containers. Kubernetes emboddies the concept of the DATA CENTER as a computer. Once provisioned, Kubernetes will abstract away the complexity of managing multiple hosts and present a multinode cluster as a single entity. A user, or an SA(service account) would POST a workload definition in the form of a yaml manifest(s) to the API server. The API server would store the manifest in its data store(ETCD) as is as a desired state. A Kubernetes control loop constantly comparing current state and desired state would notice the new definition and would engage the scheduler and the different controllers to bring the current state inline with the desired state. The desired state is reached when the number of running pods matches the number of replicas in the case of a ReplicaSets. That is everything requested in the yaml manifest including all PVCs if any are fullfilled, requested Memory, requested CPU cycles, IP addresses are assigned, DNS entries were created, endpoints if labels are matched with service labels, and Healthchecks if defined in your Pod template all pass. This mode is also refered to as a declarative model.</p>

<p>In this article I am going to demonstrate Both tools by deploying a Kubernetes cluster four different ways.</p>

   ####  RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean
   ####  Import the management of an existing GKE(Google Kubernetes Engine) to Rancher 2   


Before I start the demo, I make a few point incase you're in the process of selecting
a cloud provider to host your Kubernetes cluster(s). Kubernetes works with all current 
cloud providers. Kubernetes defines a CLOUD PROVIDER INTERFACE, a collection of well defined 
data types and interfaces and All cloud provider have to do to host Kubernetes clusters is to 
implement the interfaces and datatypes, currently all the popular Cloud provider have implemented
it, and can all host Kubernetes Clusters. However, Google Cloud Platform GCP is the only 


  
   ##### Basic Kubernetes Cluster Architecture
 
 
 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/1000_1.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/1000_1.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>





#### RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean

 The goal of this demonstration is to build a Kubernetes cluster with 1 Master(control node), 
 1 etcd(to store cluster and workload configuration) and  3 worker nodes(to run worloads).
 The Rancher Host will be running on an Ubuntu 16.04 VM with at least 4G RAM. 
 
 <lo>
   <li>Update the VM that will host the Rancher Server as follows:</li>

    # sudo apt update -y
    # sudo apt dist-upgrade -y
    # sudo apt-get remove docker docker-engine docker.io
    # sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
       
   <li>Install a supported version of Docker on the host, supported Docker versions are <code class="highlighter-rouge">1.12.6</code>, <code class="highlighter-rouge">1.13.1</code> or <code class="highlighter-rouge">17.03.2</code>. To install Docker on the server, follow the instructions from <a href="https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/">Docker</a>.</li>
 
Add Docker’s official GPG key

    # curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
Update the repository

     # sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
     # sudo apt-get update
     
Install docker-ce

    # sudo apt-get install docker-ce -y
    
Make sure that docker is running

    # systemctl status docker

 <p>Start Rancher 2.0 Server by executing the command below on your host:</p>

 <div class="highlighter-rouge"><pre class="highlight"><code><span class="gp">$ </span>sudo docker run -d --restart<span class="o">=</span>unless-stopped -p 80:80 -p 443:443 rancher/server:v2.0.0-alpha22
</code></pre>
</div>
<p>To access the Rancher UI, go to <code class="highlighter-rouge">https://&lt;SERVER_IP&gt;</code>, replacing <code class="highlighter-rouge">&lt;SERVER_IP&gt;</code> with the IP address of your host. Rancher is automatically authenticated with a default admin. You will need to log in with this user (<code class="highlighter-rouge">admin</code>) and password (<code class="highlighter-rouge">admin</code>). Upon logging in the first time, you will be asked to change the default admin’s password</p>

<blockquote>
  <p><strong>Note:</strong> Rancher only supports HTTPS and is configured, by default, with a self-signed cert.  The ability to replace this cert will be made available before GA.  Due to this, you will be prompted by your browser to trust this cert before continuing.</p>
    </blockquote>
  </li>
  <li>   

<ol>
   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official1.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official1.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


<li>Click Create Cluster:
   
   
<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official2.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official2.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="300" border="50" /></a>

   
   <li>Select DigitalOcean (default is GKE)</li>
   <li>Select Create 3 Node Pools As show below</li>
   <li>Click Add Node Template</li>
   
   
   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


 <li>Click Add Node Template (token from DigitalOcean)</li>
 
<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official4.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official4.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" /></a>
   

<li>On the DigitalOcean Dashboard generate The token<\li> 
   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/generatetoken.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/generatetoken.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" /></a>

 

<li>Paste the Token on the widow still open in Rancher 2.0 Dashboard<\li>
<li>Click Create<\li>
  


<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official6.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official6.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" /></a>





#### Click 9

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch4.png)

#### Click 10

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch5.png)

#### Click 11


   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-nodes.png)

#### Click 12

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-nodes2.png)


#### Click 13

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/clusterkubectl.png)

#### Click 14

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500002.png)







 **2.  Import the management of an existing GKE(Google Kubernetes Engine) to Rancher 2.0**
 
 In this demo, I will migrate a cluster with live workloads. The am going through the process
 of setting up a Kubernetes cluster on GCP using GKE. Just to make the demo a little interesting
 I will install a stable/wordpress HELM CHART. Use kubectl to confirm that the pods are 
 running, lookup the services, and find the ip address of the Loadbalancer assigned by GKE upon request 
 from Kubernetes on your behalf. I will point a browser to the loadbalancer address and confirm that 
 Wordpress is up and running.
 Next I will migrate the Cluster to Rancher 2.0, Confirm that all the workloads are still 
 running. Show of some of the awesomeness of Rancher 2.0, scalling up, deleting, ... you get 
 the point. I hope the illustrations are self explanatory please enjoy.
 
 

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








d















#  On digital Ocean Dashboard generate an access token  --- link to procedure.

#### Click 8

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch3.png)

#### Click 9

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch4.png)

#### Click 10

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch5.png)

#### Click 11


   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-nodes.png)

#### Click 12

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-nodes2.png)


#### Click 13

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/clusterkubectl.png)

#### Click 14

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500002.png)

#### Click 15

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500003.png)

#### Click 17

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/50000.png)

#### Click 18

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500001.png)

#### Click 19

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500004.png)

#### Click 20

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/50005.png)

#### Click 21

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500006.png)


#### Click 22

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500007.png)


#### Click 23

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500008.png)


#### Click 24

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60000.png)


#### Click 25

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60001.png)


#### Click 26

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60002.png)


#### Click 27

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60003.png)

#### Click 28

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60004.png)

#### Click 29

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60005.png)

#### Click 30

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/60006.png)





 **2.  Import the management of an existing GKE(Google Kubernetes Engine) to Rancher 2.0**
 
 In this demo, I will migrate a cluster with live workloads. The am going through the process
 of setting up a Kubernetes cluster on GCP using GKE. Just to make the demo a little interesting
 I will install a stable/wordpress HELM CHART. Use kubectl to confirm that the pods are 
 running, lookup the services, and find the ip address of the Loadbalancer assigned by GKE upon request 
 from Kubernetes on your behalf. I will point a browser to the loadbalancer address and confirm that 
 Wordpress is up and running.
 Next I will migrate the Cluster to Rancher 2.0, Confirm that all the workloads are still 
 running. Show of some of the awesomeness of Rancher 2.0, scalling up, deleting, ... you get 
 the point. I hope the illustrations are self explanatory please enjoy.
 
 

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












































**** This is a work in progress ****

**Four ways to build Kubernetes clusters with Rancher 2.0**

Making the leap to microservices is not as daunting as it was even 10 years ago. The abundance of 
information and case studies shared by many companies that went through the trials and tribulations 
helped establish solid industry best practices, coupled with many new great opensource tools donated 
or developed, and supported by a great community of contributors took a lot of the mystery out of the 
process. In this article, I am going to demonstrate two great opensource tools that I feel confident 
can make your adoption of micro-services less challenging. Kubernetes, arguably the best open source 
container orchestrator available and Rancher 2.0 to bootstrap and manage custom Kubernetes clusters.
A Kubernetes Cluster is a great habitat for micro-services. The platform provides a wealth of built 
in functionality that combined could provide limitless solutions to your micro-services design and 
deployments. Furthermore it is backed by wonderful and ambitious ecosystem.

   Comprehensive list of features that Kubernetes provides out of the box 
               to help with micro-services adoption
               
 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/microservice1.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/microservice1.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>





    - Kubernetes Master and Cluster Components -
    
 
  <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubearch2.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubearch2.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


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
would store the manifest in its data store(ETCD) as is as a desired state. A Kubernetes control 
loop constantly comparing current state and desired state would notice the new definition and would 
engage the scheduler and the different controllers to bring the current state inline with the desired state.
The desired state is reached when the number of running pods matches the number of replicas 
in the case of a ReplicaSets. That is everything requested in the yaml manifest including all PVCs if any 
are fullfilled, requested Memory, requested CPU cycles, IP addresses are assigned, DNS entries were created, 
endpoints if labels are matched with service labels, and Healthchecks if defined in your Pod template 
all pass. This mode is also refered to as a declarative model. 

    - Kubernetes Master Components
 

 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubearchi2.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubearchi2.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>



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

 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100001.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100001.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

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
keeping up with release notes. This applies to your deployments as well as manifests or helm charts you use 
to deploy your workloads. Recently I tried running helm on Version 1.9 of Kuberenetes and couldn't because of the 
change to default RBAC. I had to create and deploy the roles and rolebindings to allow tiller to access 
kube-system namespace. The moral of the storie is, you need to focus most of your efforts developing your
application and not what version goes where, and pulling your hair trying to figure out why it worked here 
and not there.

       HELM is a great opensource tool that allows packaging of kubernetes yaml manifests in a standard 
       format. It among other things defines a standard directory structure, and uses GO templating to render 
       and dynamically customize all of the yaml manifests included in the chart and their associated 
       labels, secrets, configmaps, etc. To ploy your application you simply type 
       helm install CHART-NAME        
       
         FYI:
          if helm returns the following error:
          
             - Error: configmaps is forbidden: User "system:serviceaccount:kube-system:default" 
               cannot list configmaps in the namespace "kube-system" -
               
          setup Tiller Roles and Rolebindings as shown bellow:
          
             # kubectl create serviceaccount --namespace kube-system tiller
             # kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin \
               --serviceaccount=kube-system:tiller
             # kubectl patch deploy --namespace kube-system tiller-deploy -p \
               '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'


<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/700001.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/700001.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>
  
   
   
 
 
 
 
 
 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100005.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100005.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

I made this statement earlier in the article and to be fair to GKE, I am going to highlight
some of the pros and cons of GKE as your Kubernetes Deployment method. Throughout the article,
I will try to highlight how RKE compares:


  Google Kubernetes Engine:
  
    Pros:
    *  Abstracts away and manages Kubernetes master nodes
       Nodes use
    *  container optimized image
    *  Upgrades to Kubernetes Master through GCP management console
    *  Upgrade, add, or remove worker nodes through GCP management console
    *  Global spanning load balancer built-in
    *  Easy deployment and automation
    *  Optional support for Kops
        Cons:
        * Vendor Lockin

   Rancher Kubernetes Engine

       How to: 
    *  Abstracts away and manages Kubernetes master nodes
        Nodes use
    *   Management of Adminssion rules, Network Policies, ... 
    *  container optimized image
    *  Upgrades to Kubernetes Master through GCP management console
    *  Upgrade, add, or remove worker nodes through GCP management console
    *  Global spanning load balancer built-in
    *  Easy deployment and automation

    Cons:
        * Not as stable as GKE currently but it's still early in alpha.
  




**RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean**

 The goal of this demonstration is to build a Kubernetes cluster with 1 Master(control node), 
 1 etcd(to store cluster and workload configuration) and  3 worker nodes(to run worloads). 
 The basic architecture is bellow
 
 
   
 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/1000_1.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/1000_1.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>



     From the DigitalOcean Dashboard create a VM to host the Rancher 2.0 
     server as illustrated below:



   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/first1.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/first1.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>




   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-7.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-7.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/createssh2.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/createssh2.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>



    On a terminal on your desktop create a new ssh key pair


  <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/sshkeygen1.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/sshkeygen1.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

 Copy and paste your Public key
   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100005.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/100005.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-9.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-9.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>



Verify that your newly provisioned VM is ssh accessible.
I will use the ssh private key we created in step  4 

     # ssh -i ~/.ssh/id_rsa root@<IP_ADDRESS_OF_VM_1>

   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11110.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11110.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

   
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11111.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11111.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>




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

 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/dockerstatus.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/dockerstatus.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

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
 I will install a stable/wordpress HELM CHART. Use kubectl to confirm that the pods are 
 running, lookup the services, and find the ip address of the Loadbalancer assigned by GKE upon request 
 from Kubernetes on your behalf. I will point a browser to the loadbalancer address and confirm that 
 Wordpress is up and running.
 Next I will migrate the Cluster to Rancher 2.0, Confirm that all the workloads are still 
 running. Show of some of the awesomeness of Rancher 2.0, scalling up, deleting, ... you get 
 the point. I hope the illustrations are self explanatory please enjoy.
 
 

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



















1



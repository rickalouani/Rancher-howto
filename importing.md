<h2>2 ways to build Kubernetes clusters with Rancher 2.0</h2>
<p> In this article I an going to demonstrate the capabilities of Rancher 2.0 to bootstrap, manage, Scaling 
Kuberenetes clusters. Also, how consistent it is accross Cloud Providers and allows the user to deploy Kubernetes
Cluster Vendor-lockin free</p>    
<p> Deciding between the different tools available to  to stand up Kubernetes clusters should not be trivial task. 
the 3 most widely used installers  <a href="https://github.com/kubernetes/kops" target="_blank" rel="noopener">kops</a>, 
<a href="https://github.com/kubernetes-incubator/kubespray" target="_blank" rel="noopener">Kubespray</a>, and 
<a href="https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm" target="_blank" rel="noopener">Kubeadm</a>
are great tools for standing up a cluster, and do make the process very easy. However, they are CLI based and do require 
intermediate Linux and configuration management skills, and I neither is 100% cloud agnostic. below is a brief summary
of each tool offerings and shortcomings</p>
<ol>
<li><strong>Kops</strong> is perhaps the most widely used Kubernetes installer. It is in fact much more than an installer. Kops prepares all required cloud resources, installs Kubernetes, and then wires up cloud monitoring services to ensure the continuing operation of the Kubernetes cluster. Kops is closely integrated with the underlying cloud infrastructure. Kops works the best on AWS. Support for other infrastructure platforms like GCE and vSphere is a work in progress.</li>
<li><strong>Kubespray</strong> is a popular standalone Kubernetes installer written in Ansible. It can install a Kubernetes cluster on any servers. Even though Kubespray has some degree of integration with various cloud APIs, it is fundamentally cloud independent and can, therefore, work with any cloud, virtualization clusters, or bare-metal servers. Kubespray has grown to be a sophisticated project with participation from a large community of developers.</li>
<li><strong>Kubeadm</strong> is another Kubernetes setup tool that comes with upstream Kubernetes. Kubeadm, however, does not yet support capabilities like HA clusters. Even though pieces of kubeadm code are used in projects like kops and Kubespray, kubeadm is not ready as a production-grade Kubernetes installer.</li>
</ol>
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
<li>Click Create Cluster as shown below:
   
   
<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/30002.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/30002.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

   
   <li>Select DigitalOcean (default is GKE)</li>
   <li>Select Create 3 Node Pools As show below</li>
   <li>Click Add Node Template</li>
   
<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launchcluster1.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launchcluster1.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

   <li> done</li>
</ol>
#### We need a Node Template a token in this case from DigitalOcean 

    On digital Ocean Dashboard generate an access token

 ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch2.png)

#### Click 2
<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/generatetoken.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/generatetoken.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


#### Click 4


<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nametoken.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nametoken.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


#### Click 5

    Copy and paste the token
    

#### Click 6

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/tokenlast.png)

#### Click 7

  

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














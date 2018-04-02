
# 2 ways to build Kubernetes clusters with Rancher 2.0

   In this article, I want to demonstrate how easy it is to Standup or import existing Kubernetes clusters using Rancher 2.0,
 
Standing up a Kubernetes cluster for testing or a demos like this one is simple. KUBEADM, KOPS, KUBESPRAY make standing up such a cluster a trivial task. Yet,  standing up  Secure, Highly-Available, and Fault-Tolerant production clusters 
is a more involved task . Many guides and best practices are  available to help through the process. Unfortunately, they are not simple. To use them one has to possess a deep understanding of many concepts. Concepts such as Fault Tolerance, High Availability, Disaster Recovery, security principles, and more. In most cases it is not a practical approach.  An easier approach would be to use Google Kubernetes Engine(GKE). A better approach would be to use Rancher 2.0.  Both Platform will allow you to deploy production grade Kubernetes clusters with a mouse click. Both platforms are well documented and lists of pros and cons of each platform are pervasive on the web. How they each aproach Kubernetes Cluster high availabilty and fault tolerence is what I am hoping to disuss.

Out of the box, a Kubernetes cluster would keep your workloads highly available and fault tolerant as long as its control plane and ETCD are running and resources are available, a given. But, if you are designing a production environment, that is an obvious single point of failure. I am hoping to take a birds eye view at how GKE and Rancher 2.0 aproach Cluster high availabilty and fault tolerance and hopefully give you a few pointers if you're in the process of selecting a platform. The subject is fairly complex so please bear with me if I am not able to get my points accross and cause you more confusion. But I promise I will do my best.

GKE is a managed Kubernetes service, and as the name suggests, Google manages the cluster control plane and the cluster store. Typically, you would select the number of nodes (x) click create. GKE will create 
a Kubernetes cluster with x worker nodes. Master node(s) and ETCD are managed by Google. In other words, if your master and ETCD are down you are under the mercy of Google to bring them back up. Workloads that were running will continue to run until they die which happens a lot in micro-services. Furthermore, the GKE sla defines a service level objective of  99.5 which is not ideal for a lot of use-cases. The second big drawback of GKE in my opinion is its proprietery nature. For many use cases  this is great, but if your use case calls for scaling beyond a single cluster you'll be restricted to GCP. 

A very popular method is to use Kubernetes to manage Kubernetes clusters see illustrated below.
At a high level A Kubernetes API server is presented to the users as a single API front end to recieve all API requests and forwards, them to every cluster. A controller manager coordinates with the API server manages across all clusters and routes requests to the individual API servers.




<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/federation.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/federation.png" 
alt="IMAGE ALT TEXT HERE" width="640" height="320" border="50"/></a>


#### Using Kubernetes to Manage Kubernetes Clusters _Cluster_ _Federation_

Rancher 2.0 provides you with a platform where you can truly customize your cluster, it allows you to design your cluster using industry best practices. It also allows you to scale not only your worker nodes but control nodes as well accross any cloud provider. It allow more control over Kubernetes clusters than most of us need all from a single UI or CLI. Rancher 2.0 uses a similar aproach as seen in the illustration below. It also includes a Kubernetes API server, and controller in its solution. It also leverages Kubernetes CRD(custom resource definition formally know as thirdparthy resources) as part of it's solution.
 
#### Rancher 2.0 also uses a Kubernetes API server for cluster management

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/rancher-highlevel.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/rancher-highlevel.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


## RKE(Rancher Kubernetes Engine) to build a 5 node cluster on DigitalOcean

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

 

<li>Paste the Token on the widow still open in Rancher 2.0 Dashboard</li>
<li>Click Create</li>
<li> when the cluster is ready click on the cluster to see the dashboard</li>


<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official6.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official6.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" /></a>





#### The 5 nodes that Rancher created on DigitalOcean for us:

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launch4.png)


#### A view of the cluster from the UI


   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-nodes.png)

#### Nodes with kubectl get nodes
     # kubectl get nodes

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-nodes2.png)


#### Pods, Services, Namespaces, and configmaps out of the box.

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/clusterkubectl.png)







## Import an existing GKE to Rancher 2.0

 The goal of this demonstration is to import a basic 3 Node GKE cluster with all GKE
 defaults as shown below. 
       - Note that GKE enables Kubernetes Dashboard, Heapster, and, Fuentd by default. 
      
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nodes-and-config.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nodes-and-config.png" /></a>


<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/allpods-gke.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/allpods-gke.png" /></a>

 
#### 1. _Log on to the Rancher Server UI_

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster.png" alt="IMAGE ALT TEXT HERE" width="440" height="340" border="50" /></a>


#### 2. _Click Add Cluster_
   
 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/add-cluster.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/add-cluster.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

#### 3. Select _Import_   

#### 4. Select a Name for the cluster and click _Create_                                                                                         
#### 5. for this demo I am using a self-signed SSL certificate please 
####  _Copy_ one of the 2 options as shown below 

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/select-option.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/select-option.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


#### 6. Now on a terminal configure with GKE kubecfg and _Paste_ the command copied above: 

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-created.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-created.png" 
 /></a>

#### 7. Head on over to Rancher 2.0 UI to inspect the cluster

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-done.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-done.png" 
 /></a>


#### 8. Locate the cluster's _Kubeconfig_ file as show above and _Click_ on _it_   

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png" 
 /></a>

#### 9. Copy and Paste the file to ~/.kube/config on a host with kubectl _INSTALLED_ 

##### Please notice the extra namespace and Pods that Rancher 2.0 added to the cluster more about that later.
  

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/added-pod.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/added-pod.png" 
/></a>

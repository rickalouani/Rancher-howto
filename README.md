
<h2>2 ways to build Kubernetes clusters with Rancher 2.0</h2>

Standing up a cluster for testing or a demos is fairly simple. Tools such as KUBEADM, KOPS, KUBESPRAY make standing up such a cluster a relatively trivial task. However, standing up an ideal production cluster is a very tall task. Many guides and best practices and reference designs are available to help you if you decide to go that route. However, keep in mind that they are
not simple guides and do require some level of proficiency. They require a deep understanding of Fault tolerance, High Availability, disaster recovery, and security principles and possibly more. In most cases it is not a practical aproach.
An easy aproach is to use GKE (Google Kubernetes Engine) to standup your clusters. GKE offers a wealth of benefits including:

<h3>Its key features include:</h3>
<ul>
<li>Security is handled by the Google security team.</li>
<li>Compliance with HIPAA and PCI DSS is already managed.</li>
<li>Kubernetes instances are fully clustered, and will auto scale.</li>
<li>It’s based on the upstream Kubernetes project, which enables workload portability to other Kubernetes instances, whether they are another cloud provider or on-premises.</li>
</ul>


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

#### Click 14

   ![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/500002.png)







#### Import an existing GKE to Rancher 2.0

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

###### Please notice the extra namespace and Pods that Rancher 2.0 added to the cluster more about that later.
  

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/added-pod.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/added-pod.png" 
/></a>

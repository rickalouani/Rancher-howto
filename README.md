
# 2 ways to build Kubernetes clusters with Rancher 2.0

   We all heard that standing up a Kubernetes cluster for testing or a demos like this one is fairly simple. Tools such as KUBEADM, KOPS, KUBESPRAY make standing up such a cluster a relatively trivial task. However, standing up a secure, highly available, and fault tolerant _production_ _cluster_ is a very tall task. Many guides and best practices and reference designs are available to help you if you decide to go that route. But, keep in mind that they are not simple guides and do require some level of proficiency. They require a good understanding of Fault tolerance, High Availability, disaster recovery, and security principles and possibly more. In most cases it is not a practical aproach.
An easier aproach is to use GKE (Google Kubernetes Engine) to standup your clusters. GKE offers a wealth of benefits and handles all the security, provides an ok (99.5) high availability, and depending on your definition also faultolerance.
the sla is here <put sla here>. But, GKE allow little to no control over the master components. When you create a cluster,
GKE spins up a master and configures the nodes you defined as worker-nodes. Think about it, if you were to decide to switch cloud providers, I am not sure what the process is to get a copy of your clusters ETCD from google to use it to start a new cluster elsewhere. I don't know about you, but if I standup a Kubernetes cluster I want control over all components, the abilty to add more nodes on any cloud platform.

   An Openstource tool that provides arguably all the services that GKE provides. It also provides controll over the whole cluster(s), and is cloud provider agnostic. It also gives you a single UI or a single CLI to manage all you clusters. 
In this article, I want to demonstrate how easy it is to Standup or import existing Kubernetes clusters using Rancher 2.0, 
and hopefully entice you to try it and experiment with it.



##  RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean

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

###### Please notice the extra namespace and Pods that Rancher 2.0 added to the cluster more about that later.
  

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/added-pod.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/added-pod.png" 
/></a>

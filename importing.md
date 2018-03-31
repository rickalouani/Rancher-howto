## Import an existing GKE to Rancher 2.0

 The goal of this demonstration is to import a basic 3 Node GKE cluster with all GKE
 defaults as shown below. 
       - Note that GKE enables Kubernetes Dashboard, Heapster, and, Fuentd by default. 
      
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nodes-and-config.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nodes-and-config.png" /></a>


<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/allpods-gke.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/allpods-gke.png" /></a>

 
### 1. _Log on to the Rancher Server UI_

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/create-cluster.png" alt="IMAGE ALT TEXT HERE" width="440" height="340" border="50" /></a>


### 2. _Click Add Cluster_
   
 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/add-cluster.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/add-cluster.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

### 3. Select _Import_   

### 4. Select a Name for the cluster and click _Create_                                                                                         
### 5. for this demo I am using a self-signed SSL certificate please 
### _Copy_ one of the 2 options as shown below 

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/select-option.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/select-option.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


### 6. Now on a terminal configure with GKE kubecfg and _Paste_ the command copied above: 

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-created.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-created.png" 
 /></a>

### 7. Head on over to Rancher 2.0 UI to inspect the cluster

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-done.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-done.png" 
 /></a>


### 8. Locate the cluster's _Kubeconfig_ file as show above and _Click_ on _it_   

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png" 
 /></a>

### 9. Copy and Paste the file to ~/.kube/config on a host with kubectl _INSTALLED_ 

#### Please notice the extra namespace and Pods that Rancher 2.0 added to the cluster.
#### more about that later.

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/added-pod.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/added-pod.png" 
/></a>



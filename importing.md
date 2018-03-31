## Import an existing GKE to Rancher 2.0

 The goal of this demonstration is to import a basic 3 Node GKE cluster with all GKE
 defaults as shown below. 
       - Note that GKE enables Kubernetes Dashboard, Heapster, and, Fuentd by default. 
      
   <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nodes-and-config.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nodes-and-config.png" /></a>

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/allpods-gke.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/allpods-gke.png" /></a>

 
### 1. _Log on to the Rancher Server UI
### 2. _Click Add Cluster_
   
 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>
### 3. _Select Import_ 
   
### 4. _Select a Name for the cluster and click create_

 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>Select Import .                                                                                                    
### 5. for this demo I am using a self-signed SSL certificate please _copy one of the 2 options_ as shown below 

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

### 6. Now on a terminal configure with GKE kubecfg and _Paste_ the command as shown below 

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-done.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/cluster-done.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

### 7. Head on over to Rancher 2.0 UI to inspect the cluster

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>


### 8. Locate the cluster's _Kubeconfig file_ as show above and click on it   

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

### 9. Copy and Paste the file to ~/.kube/config     

<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>



<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>




<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>



<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubeconfig.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

  

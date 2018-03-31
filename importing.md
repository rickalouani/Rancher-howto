## Import an existing GKE to Rancher 2.0

 The goal of this demonstration is to import a basic 3 Node GKE cluster with all GKE
 defaults as shown below. 
      - Note that GKE enables Kubernetes Dashboard, Heapster, and, Fuentd by default. 
      
      <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>

 
   ### 1. Log on to the Rancher Server UI
   ### 2. Click Add Cluster
   
 <a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/official3.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>
   ### 3. Select _Add Cluster_                                                                          
Select Import .                                                                                                     picture.png
Select a Name for the cluster and click create
for this demo I am using a self-signed SSL certificate please copy one of the 2 options as shown below options.png    
Now on a terminal configure with GKE kubecfg and paste the command as shown below cluster-created.png      
Head on over to Rancher 2.0 UI to inspect the cluster        cluster-done.png
Locate the cluster's Kubeconfig file as show above and click on it          kubeconfig.png
 Copy and Paste the file to ~/.kube/config       
  

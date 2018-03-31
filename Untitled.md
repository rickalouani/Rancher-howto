In this demo, I am going to import a basic 3 GKE cluster, all GKE

defaults as shown below. 

  - Note that GKE enables Kubernetes Dashboard, Heapster, and .           Fuentd by default. 

nodes-and-pods.png

1.  Log on to the Rancher Server UI
2.  Click Add Cluster                                                                                    add-cluster.png
3.  Select Import .                                                                                                     picture.png
4.  Select a Name for the cluster and click create
5.  for this demo I am using a self-signed SSL certificate please **copy** one of the 2 options as shown below options.png    
6.  Now on a terminal configure with GKE kubecfg and **paste** the command as shown below cluster-created.png      
7.  Head on over to Rancher 2.0 UI to inspect the cluster        cluster-done.png
8.  Locate the cluster's Kubeconfig file as show above and click on it          kubeconfig.png
9.  **Copy** and **Paste** the file to ~/.kube/config          

added-pods.
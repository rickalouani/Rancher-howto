<h2>2 ways to build Kubernetes clusters with Rancher 2.0</h2>
<p>The tools avaialbe for bootstraping a Kubernetes cluster such as KOPS, KUBEADM kubespray, KUBEADM are great tools for standing up a cluster, and do make the process very easy. However, they are CLI based and do require Linux and configuration management skills.</p>

<p>Users who want to stand up Kubernetes clusters today have many choices of installers. Two of the most popular installers we have encountered are <a href="https://github.com/kubernetes/kops" target="_blank" rel="noopener">kops</a> and <a href="https://github.com/kubernetes-incubator/kubespray" target="_blank" rel="noopener">Kubespray</a>:</p>
<ol>
<li><strong>Kops</strong> is perhaps the most widely used Kubernetes installer. It is in fact much more than an installer. Kops prepares all required cloud resources, installs Kubernetes, and then wires up cloud monitoring services to ensure the continuing operation of the Kubernetes cluster. Kops is closely integrated with the underlying cloud infrastructure. Kops works the best on AWS. Support for other infrastructure platforms like GCE and vSphere is a work in progress.</li>
<li><strong>Kubespray</strong> is a popular standalone Kubernetes installer written in Ansible. It can install a Kubernetes cluster on any servers. Even though Kubespray has some degree of integration with various cloud APIs, it is fundamentally cloud independent and can, therefore, work with any cloud, virtualization clusters, or bare-metal servers. Kubespray has grown to be a sophisticated project with participation from a large community of developers.</li>
<<li><strong>Kubeadm</strong> is another Kubernetes setup tool that comes with upstream Kubernetes. Kubeadm, however, does not yet support capabilities like HA clusters. Even though pieces of kubeadm code are used in projects like kops and Kubespray, kubeadm is not ready as a production-grade Kubernetes installer.</li>
</ol>
<p> At a basic level a Kubernetes cluster is a collection of resources such as hosts, CPU cores, storage, and Memory and technologies such as Containerization, SDN/CNI, and RESTful API design all integrated together to give an abstraction layer one level above the container run time. With this abstraction, a kubernetes cluster could be viewed as a number of container run time connected together using an Overlay Network and presented to PODs as a single run time. A Pod is the smallest unit of scheduling in Kubernetes, and could be viewed as a packaged container(s) and a thin wrapper to allow Kubernetes to manage PODs not Containers. Kubernetes emboddies the concept of the DATA CENTER as a computer. Once provisioned, Kubernetes will abstract away the complexity of managing multiple hosts and present a multinode cluster as a single entity. A user, or an SA(service account) would POST a workload definition in the form of a yaml manifest(s) to the API server. The API server would store the manifest in its data store(ETCD) as is as a desired state. A Kubernetes control loop constantly comparing current state and desired state would notice the new definition and would engage the scheduler and the different controllers to bring the current state inline with the desired state. The desired state is reached when the number of running pods matches the number of replicas in the case of a ReplicaSets. That is everything requested in the yaml manifest including all PVCs if any are fullfilled, requested Memory, requested CPU cycles, IP addresses are assigned, DNS entries were created, endpoints if labels are matched with service labels, and Healthchecks if defined in your Pod template all pass. This mode is also refered to as a declarative model.</p>

<p>In this article I am going to demonstrate Both tools by deploying a Kubernetes cluster four different ways.</p>



<h2>How RKE Works</h2>
<p>RKE is a standalone executable that reads from a cluster configuration file and brings up, brings down, or upgrades a Kubernetes cluster. Here is a sample configuration file:</p>
<pre>---

<p>In the last two years, Rancher has become one of the most popular ways to stand up and manage Kubernetes clusters. Users love Rancher as a Kubernetes installer because it is very easy to use. Rancher fully automates etcd, the Kubernetes master, and worker node operations. Rancher 1.x, however, also implements container networking. Therefore, a failure of the Rancher management plane could disrupt the operation of the Kubernetes cluster.</p>
<p>Users who want to stand up Kubernetes clusters today have many choices of installers. Two of the most popular installers we have encountered are <a href="https://github.com/kubernetes/kops" target="_blank" rel="noopener">kops</a> and <a href="https://github.com/kubernetes-incubator/kubespray" target="_blank" rel="noopener">Kubespray</a>:</p>
<ol>
<li><strong>Kops</strong> is perhaps the most widely used Kubernetes installer. It is in fact much more than an installer. Kops prepares all required cloud resources, installs Kubernetes, and then wires up cloud monitoring services to ensure the continuing operation of the Kubernetes cluster. Kops is closely integrated with the underlying cloud infrastructure. Kops works the best on AWS. Support for other infrastructure platforms like GCE and vSphere is a work in progress.</li>
<li><strong>Kubespray</strong> is a popular standalone Kubernetes installer written in Ansible. It can install a Kubernetes cluster on any servers. Even though Kubespray has some degree of integration with various cloud APIs, it is fundamentally cloud independent and can, therefore, work with any cloud, virtualization clusters, or bare-metal servers. Kubespray has grown to be a sophisticated project with participation from a large community of developers.</li>
</ol>
<p><a href="https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/" target="_blank" rel="noopener">Kubeadm</a> is another Kubernetes setup tool that comes with upstream Kubernetes. Kubeadm, however, does not yet support capabilities like HA clusters. Even though pieces of kubeadm code are used in projects like kops and Kubespray, kubeadm is not ready as a production-grade Kubernetes installer.</p>
<p><a href="http://rancher.com/rancher2-0/" target="_blank" rel="noopener">Rancher 2.0</a> is designed to work with any Kubernetes clusters. We encourage users to leverage cloud-hosted Kubernetes services like GKE and AKS. For users who want to set up their own clusters, we considered incorporating either kops or Kubespray into our product lineup. Kops does not suit our needs because it does not work with all cloud providers. Kubespray is in fact very close to what we want. We especially like how Kubespray can install Kubernetes anywhere. In the end, we decided not to use Kubespray and instead build our own lightweight installer for two reasons:</p>
<ol>
<li>We can have a simpler system by starting from scratch and take advantage of many advances in Kubernetes itself.</li>
<li>We can have a faster installer by going with a container-based approach, just like how we installed Kubernetes in Rancher 1.6.</li>
</ol>
<h2>How RKE Works</h2>
<p>RKE is a standalone executable that reads from a cluster configuration file and brings up, brings down, or upgrades a Kubernetes cluster. Here is a sample configuration file:</p>
<pre>---







<p>You can download RKE from <a href="https://github.com/rancher/rke/releases" target="_blank" rel="noopener">GitHub</a>. I encourage you to read the <a href="http://rancher.com/an-introduction-to-rke/" target="_blank" rel="noopener">blog post</a> by Hussein Galal, who wrote a significant portion of RKE code, for a more in-depth introduction to RKE.</p>









Emphasis, aka italics, with *asterisks* or _underscores_.

Emphasis, aka italics, with *asterisks* or _underscores_.

       Strong emphasis, aka bold, with **asterisks** or __underscores__.

Strong emphasis, aka bold, with **asterisks** or __underscores__.

      Combined emphasis with **asterisks and _underscores_**.

Combined emphasis with **asterisks and _underscores_**.


Strikethrough uses two tildes. ~~Scratch this.~~


# H1
## H2
### H3
#### H4
##### H5
###### H6

Alternatively, for H1 and H2, an underline-ish style:

Alt-H1
======

Alt-H2
------










<a href="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/microservice1.png
" target="_blank"><img src="https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/microservice1.png" 
alt="IMAGE ALT TEXT HERE" width="540" height="320" border="50" /></a>






> This is a very long line that will still be quoted properly when it wraps. Oh boy let's keep writing to make sure this is long enough to actually wrap for everyone. Oh, you can *put* **Markdown** into a blockquote.  
  
  
  
  
  
  
  
  
  Colons can be used to align columns.

    | Tables        | Are           | Cool  |
    | ------------- |:-------------:| -----:|
    | col 3 is      | right-aligned | $1600 |
    | col 2 is      | centered      |   $12 |
    | zebra stripes | are neat      |    $1 |




Colons can be used to align columns.

| Micro-services Core Needs             |          Kubernetes offerings   |
| --------------------------------------|:-------------------------------:|
| col 3 is                              |                                 |  
| 
Markdown | Less | Pretty
--- | --- | ---
*Still* | `renders` | **nicely**
1 | 2 | 3







Here's our logo (hover to see the title text):

    Inline-style: 
    ![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")

Inline-style: 
    ![alt text](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-12.png "Logo Title Text 1")
    
    
    Reference-style: 
    ![alt text][logo]

    [logo]: https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-6.png "Logo Title Text 2"


Reference-style: 
![alt text][logo]

[logo]: https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-6.png "Microservices"




      1. [I'm an inline-style link](https://www.google.com)
[I'm an inline-style link](https://www.google.com)

     2. [I'm an inline-style link with title](https://www.google.com "Google's Homepage")
     
[I'm an inline-style link with title](https://www.google.com "Google's Homepage")

     3. [I'm a reference-style link][Arbitrary case-insensitive reference text]
     
[I'm a reference-style link][Arbitrary case-insensitive reference text]
     
     
     4. [I'm a relative reference to a repository file](../blob/master/LICENSE) 

[I'm a relative reference to a repository file](../blob/master/LICENSE)

     5. [You can use numbers for reference-style link definitions][1]
 
[You can use numbers for reference-style link definitions][1]

    6. [You can use numbers for reference-style link definitions][1]
    
[You can use numbers for reference-style link definitions][1]


Or leave it empty and use the [link text itself].

URLs and URLs in angle brackets will automatically get turned into links. 
http://www.example.com or <http://www.example.com> and sometimes 
example.com (but not on Github, for example).

Some text to show that the reference links can follow later.

[arbitrary case-insensitive reference text]: https://www.mozilla.org
[1]: http://slashdot.org
[link text itself]: http://www.reddit.com

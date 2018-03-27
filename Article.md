**** This is a work in progress ****

**Four ways to build Kubernetes clusters with Rancher 2.0**

Making the leap to microservices is not as daunting as it was even
10 years ago. The abundance of information and case studies shared by many 
companies that went through the trials and tribulations helped establish 
solid industry best practices. Coupled with many new great opensource tools 
Making the leap to micro-services is not as daunting as it was even 10 years 
ago. The abundance of information and case studies shared by many companies 
that went through the trials and tribulations helped establish solid industry 
best practices. Coupled with many new great open-source tools donated or 
developed and supported by a great community of contributors took the mystery 
out of the process. In this article, I am going to demonstrate two great 
opensource tools that I am confident can make your adoption of micro-services 
less challenging. Kubernetes, arguably the best open source container 
orchestrator available and Rancher 2.0 to bootstrap and manage custom Kubernetes
clusters.  
A Kubernetes Cluster is a great habitat for micro-services. The platform 
provides a wealth of built in functionality and solutions and supported by
a wonderful opensource community and a huge ecosystem. It also provides a great 
API that you can leverage to customize your clusters or to use as a baseline for
your own [extending Kubernetes]. 
Rancher 2.0 as I will demonstrate makes customizing, bootstrapping, and managing
Kubernetes clusters a breeze. It also cleans up nicely with a simple point and click.
A very big plus in my opinion because if you're paying for resources outside of kubernetes control
like load balances, floating/elastic IPs will not go away when you delete the kubernetes cluster. 
They can linger around and incurs unecessary expenses until they are found and deleted.

In this article I am going to demonstrate Both tools by deploying a Kubernetes cluster four 
different ways:

**1. RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean**

**2.  Import the management of an existing GKE(Google Kubernetes Engine) to Rancher 2.0**

**3.  RKE on AWS**  

**4.  Custom cluster using VMs**

Many of the early adopters were thriving companies (Amazon, Google,
Netflix, ...), growing fast and the outlook was great. They were having to 
scale up their resources to meet demand, unfortunately most the scaling was 
Vertical. It became apparent to them that adding more data centers to meet 
demand was cost prohibitive, and logistically not practical. Furthermore, some 
of these companies' own metrics were showing capacity usage hovering around 
20% at peak. They realized they were heading towards a scalability nightmare, 
and who wouldn't. Conceptually, it took them 5 data centers to service the same 
number of requests as would a single data center where resources are managed 
appropriately. The problem was clearly defined CPU core, and Memory need to be 
managed better, simple, right? 
To make a long story short, The problem was a direct result of the monolithic 
architecture used to develop software. The way applications were developed, 
built, packaged, and executed was at the heart of the problem. An application 
was the end product of thousands of line of code developed over many years by 
many developers, compiled, and built into an executable, and packaged together 
with library files. When the application is installed and executed as a 
monolith that runs and dies as a single entity, allocating resources for it 
was the responsibility of OS and under the mercy of the quality of the source code. 
Functions within the application, shared the same CPUs, Shared Memory, and Storage local 
or remote. When whole systems were crashing because of a memory leak caused by some badly 
written trivial function It became obvious that any solution selected had to include
a new software design paradigm where applications are divided into smaller more managable 
modules that could be managed independetly(containers) and the ability to assign resources 
at a finer granularity to application modules directly.
The current covention is as follows:
   * A CPU core is divided into units of m (millicpu). The idea is quite simple, 
     when a function foo is assigned 200m vCPU, all it means is that 
     the underlying OS scheduler is supposed to service our function requests at 
     least 20% of of the time while running. If foo runs for the 5 min we can be 
     confident that foo received least one minute of CPU, if the system is not busy,
     the OS might give foo more resources but that is not guaranteed. 
   * Memory and Storage are straight forward with only one minor detail, the units are in bytes 
     and you can specify exact number like 1G(billion bytes) E,P,T,G,M,K or in binary 
     1Gi(gigabyte) Ei, Pi, Ti, Gi, Mi, Ki. The goal was the ability to assign exact 
     dedicated resources to application functions vs all the functions sharing single 
     resources. 
The overall solution they each came up with shared similar concepts and directly 
Containers and Container orchestration. Linux provided the tools necessary(NAMESPACES, 
CGROUPS,) to provide separation of concerns one of the core principle in the 
the micro-services architecture. Applications had to be divided into smaller 
more manageable services that could be developed independently and communicate
with each other using a common API. However, breaking up the monolith did not 
simplify the situation, it complicated it enormously. With a monolith, you 
install it configure it and run it. All the pieces are built and packaged 
together (executables and libraries). interactions between application modules 
was straight forward because all the pieces were installed together on the same 
host and share the same OS resources such as RPC, thread management, message 
queues, etc. The OS was also responsible for Scheduling and managing resources.
Breaking down the monolith presented a different challenge. How are all these 
newly packages pieces of my application are going to communicate and work with 
each other to produce at least the same functionality and reliability my 
monolith? if these micro-services can run independently and can be assign 
resources why not go further and develop the ability to have them run on 
different hosts, maybe even on different networks. The result is containers and 
container orchestration. Kubernetes was devolopped by google to solve the 
container orchestration puzzle. What I am hoping to accomplish here in this 
article is to help you setup a Kubernetes cluster to help give you a test bed 
where you can experiment with containers as you create them. You will have a 
play ground where you can take your newly made containers on test drives. 
The Diagram below highlights some of Kubernetes features and how it provides
a solid platform for a successful migration to micro-services.

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/microservice1.png)

A Kubernetes cluster is a collection of resources(hosts, storage, and great 
technologies(docker, SDN, CNI, RESTful API design) integrated together 
beautifully.Kubernetes emboddies the concept of the DATA CENTER as a computer. 
Once provisioned, Kubernetes will abstract away the complexity of managing 
multiple hosts and present a multinode cluster as a single entity. A user would 
submit a desired state of workloads in the form of a yaml manifest and the API server 
will store the manifest in it's data store ETCD. When the scheduler finds a node with 
enough free resources to satisfy a request the workload would get scheduled on that node.
A watch loop constantly compares the current state and the desired state(stored in ETCD)
and if they vary kubelet(kubernetes agent on the nodes) will inform the API server.
The API server will engage the appropriate controller to bring the current state in line with
the desired state.

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubedesign1.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/kubearchi2.png)

The tools avaialbe for bootstraping a Kubernetes cluster such as KOPS, 
KUBEADM, conjure-up, kube-spray, and MINIKUBE are great tools for standing up 
a cluster, and do make the process very easy. However, they are CLI based,  
and do require Linux skills and configuration and scripts would have to be version controlled. 
In this article I would love to demonstrate Rancher 2.0  you to give
[Rancher 2.0]: http://rancher.com/rancher2-0/  a try as a tool to bootstrap new or import 
existing Kubernetes clusters and Manage them through a friendly and very intuitive WebUI or 
Rancher 2.0 CLI. 

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/addnodes.png)

provides the user with access to configuration variables, API endpoints, among other things
its own Kuberentes Engine RKE(Rancher Kubernetes Engine), and allows the user 
to create, manage, and monitor multiple kubernetes clusters across different cloud 
providers from a single UI. It also allows a user to import existing Kubernetes clusters 
as I will demonstrate later in the article.

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/rancher-2.0.png)




Along the way, I will highlight some of the pros and cons of each method of deployment.

*   assumption: Google Cloud Platform, and, or AWS account is already setup. < I will add links to how-to-docs>

*   assumption: User has already configured Rancher 2.0 < will add links to how-to-docs>


**RKE(Rancher Kubernetes Engine) to build a 5 node cluster from scratch on DigitalOcean**

 The goal of this demonstration is to build a Kubernetes cluster with 1 Master(control node), 
 1 etcd(to store cluster and workload configuration) and  3 worker nodes(to run our microservices). 
 The basic architecture is bellow
 
 
![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/1000_1.png)



From the DigitalOcean Dashboard create a VM to host the Rancher 2.0 server as illustrated below:


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/first1.png)


2.

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-7.png)

3.

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/createssh2.png)


**4. On a terminal on your desktop create a new ssh key pair** 


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-5.png)

**5. Copy and paste your Public key**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/sshkeygen3.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/10000-9.png)


Verify that your newly provisioned VM is ssh accessible.
I will use the ssh private key we created in step  4
 **ssh -i ~/.ssh/id_rsa root@<IP_ADDRESS_OF_VM_1>**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11110.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/11111.png)

Now let's update the distribution, repositories and install Docker

 **Note:** I am going to use docker community edition but you are free to use any of the versions supported by Rancher 2.0)
supported docker versions: http://rancher.com/docs/rancher/v1.6/en/hosts/#supported-docker-versions
  

  **# sudo apt update -y**
  **# sudo apt dist-upgrade -y**
  **# sudo apt-get remove docker docker-engine docker.io**

  **# sudo apt-get install \**
  **apt-transport-https \**
  **ca-certificates \**
  **curl \**
  **software-properties-common**

 - Add Docker’s official GPG key

# curl -fsSL \
https://download.docker.com/linux/ubuntu/gpg | \
sudo apt-key add -

- Update the repository
**# sudo add-apt-repository \**
**"deb [arch=amd64] https://download.docker.com/linux/ubuntu \**
**$(lsb_release -cs) \**
**stable"**

**# sudo apt-get update**
- Install docker-ce
**# sudo apt-get install docker-ce -y**

- Make sure that docker is running
**# systemctl status docker**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/dockerstatus.png)
- Run the Rancher server container as follows
**# sudo docker run -d --restart=unless-stopped -p 80:80 -p 443:443 rancher/server:preview**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/serverrunning.png)
 


**point you browser to the ip address of your Rancher 2.0 server**
**Accept the certificate and enter the default user name and password (admin admin)**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/30001.png)

**Click Create Cluster**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/30002.png)


![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/launchcluster1.png)

We need a Node Template a token in this case from DigitalOcean 

**On digital Ocean Dashboard generate an access token**

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/generatetoken.png)

![](https://github.com/rickalouani/Rancher-howto/blob/master/Rancher-screen-shots/nametoken.png)

**Copy and paste the token**

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



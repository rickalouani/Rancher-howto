
<p>In this guide, you’ll learn how to get started with Rancher v2.0, including:</p>

<ul>
  <li>Preparing a Linux Host</li>
  <li>Launching Rancher Server and Accessing the Rancher UI</li>
  <li>Creating Clusters through the Rancher UI</li>
  <li>Importing an Existing Kubernetes Cluster</li>
  <li>Adding a Pod through the Rancher UI</li>
  <li>Using Kubeconfig file</li>
</ul>

<p><a id="prepare-host"></a></p>

<h3 id="preparing-a-linux-host">Preparing a Linux Host</h3>

<ol>
  <li>Prepare a Linux host with 64-bit Ubuntu 16.04, at least 4GB of memory.</li>
  <li>Install a supported version of Docker on the host, supported Docker versions are <code class="highlighter-rouge">1.12.6</code>, <code class="highlighter-rouge">1.13.1</code> or <code class="highlighter-rouge">17.03.2</code>. To install Docker on the server, follow the instructions from <a href="https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/">Docker</a>.</li>
</ol>

<p><a id="launch-rancher"></a></p>

<h3 id="creating-rke-clusters">Creating RKE Clusters</h3>

<p>You can create a cluster using <a href="https://github.com/rancher/rke">RKE</a>, which will install Kubernetes on any nodes that you bring into your cluster. You can add node(s) from various cloud providers that Rancher v2.0 support and specify the role for each node for the Kubernetes cluster.</p>

<blockquote>
  <p><strong>Note:</strong> Currently in the tech preview, only Digital Ocean and AWS nodes are supported, but support for all the cloud providers and adding custom nodes will be added.</p>
</blockquote>

<h3 id="launching-rancher-server">Launching Rancher Server</h3>

<p>It only takes one command and less than a minute to install and launch Rancher Server. Once installed, you can open a web browser to access the Rancher UI.</p>

<h4 id="to-launch-rancher-server">To Launch Rancher Server:</h4>

<ol>
  <li>
    <p>Run this Docker command on your host:</p>

   <div class="highlighter-rouge"><pre class="highlight"><code><span class="gp">$ </span>sudo docker run -d --restart<span class="o">=</span>unless-stopped -p 80:80 -p 443:443 rancher/server:preview
</code></pre>
    </div>
  </li>
  <li>
    <p>To access the Rancher UI, go to <code class="highlighter-rouge">https://&lt;SERVER_IP&gt;</code>, replacing <code class="highlighter-rouge">&lt;SERVER_IP&gt;</code> with the IP address of your host. Rancher is automatically authenticated with a default admin. You will need to log in with this user (<code class="highlighter-rouge">admin</code>) and password (<code class="highlighter-rouge">admin</code>). Upon logging in the first time, you will be asked to change the default admin’s password.</p>

   <blockquote>
      <p><strong>Note:</strong> Rancher only supports HTTPS and is configured, by default, with a self-signed cert.  The ability to replace this cert will be made available before GA.  Due to this, you will be prompted by your browser to trust this cert before continuing.</p>
    </blockquote>
  </li>
  <li>
    <p>Start adding clusters into your Rancher server. Select one of the options for adding clusters, and continue to the relevant section below:</p>

  <ul>
    <li><strong>Creating a Cloud Cluster</strong> – Select this option to create and use a new cluster and nodes managed by a cloud Kubernetes provider. To create a new cloud cluster, go to <a href="#creating-cloud-clusters"><strong>Creating Cloud Clusters</strong></a>.</li>
      <li><strong>Creating a <a href="https://github.com/rancher/rke">RKE</a> Cluster</strong> – Select this option to have Rancher deploy and automatically manage a Kubernetes cluster for you. To create a RKE cluster, go to <a href="#creating-rke-clusters"><strong>Creating RKE Clusters</strong></a>.</li>
      <li><strong>Import an Existing Kubernetes Cluster</strong> – Select this option if you want the cluster provider to manage hosts outside Rancher. To import an existing Kubernetes installation, go to <a href="#importing-kuberentes-clusters"><strong>Importing Kubernetes Clusters</strong></a>.</li>
    </ul>
  </li>
</ol>

<h3 id="creating-cloud-clusters">Creating Cloud Clusters</h3>

<p>In Rancher v2.0, you can create new Kubernetes clusters from hosted services like Google Container Engine (GKE).</p>

<ol>
  <li>
    <p>Click on the <strong>Add Cluster</strong> button and in “Create a Cloud Cluster”, click on <strong>Select</strong>.</p>
  </li>
  <li>
    <p>Follow the instructions in the Rancher UI to create and add your cluster. The provisioning process might take a few minutes to complete. Once your cluster is ready, you can view its status on the Clusters page. Once your cluster is running, Rancher creates a <code class="highlighter-rouge">Default</code> project and a <code class="highlighter-rouge">default</code> namespace. Once the cluster is active, you can start adding pods into your namespace.</p>
  </li>
</ol>

<h3 id="creating-rke-clusters">Creating RKE Clusters</h3>

<p>You can create a cluster using <a href="https://github.com/rancher/rke">RKE</a>, which will install Kubernetes on any nodes that you bring into your cluster. You can add node(s) from various cloud providers that Rancher v2.0 support and specify the role for each node for the Kubernetes cluster.</p>

<blockquote>
  <p><strong>Note:</strong> Currently in the tech preview, only Digital Ocean and AWS nodes are supported, but support for all the cloud providers and adding custom nodes will be added.</p>
</blockquote>

<!--
If you're adding a custom host, note these requirements:

* Typically, Rancher automatically detects the IP address to register the host.
  * If the host is behind a NAT or the same machine that is running the `rancher/server` container, you might need to explicitly specify its IP address. To do so, click **Show advanced options**, and then enter the **Registration IP Address**.
* The host agent initiates a connection to the server, so make sure firewalls or security groups allow it to reach the URL in the command.
* All hosts in the environment must to allow traffic between each other for cross-host networking
  * IPSec: `500/udp` and `4500/udp`
  * VXLAN: `4789/udp`
-->

<ol>
  <li>
    <p>Follow the instructions in the Rancher UI to create and add your RKE cluster.</p>
  </li>
  <li>
    <p>In the Nodes section, click on <strong>Add a new node</strong> to select which type of node you want to add. You can configure a new node template to launch nodes or select from an existing node template (i.e. if you had previously launched a node). When launching any new nodes, a node template is saved with your configuration to allow you to re-use this configuration for adding additional nodes.</p>
  </li>
  <li>
    <p>After selecting your node(s) to be created, select which roles you want the node(s) to act in the Rancher managed Kubernetes cluster.</p>

   <p>Select from the following roles:</p>

   <ul>
      <li><strong>etcd</strong> – On this node, <code class="highlighter-rouge">etcd</code> is launched. Etcd is a distributed reliable key-value store which stores all Kubernetes state. We recommend running 1, 3, or 5 nodes with the etcd role.</li>
      <li><strong>management</strong> – On this node, master components will run (<code class="highlighter-rouge">kube-api</code>, <code class="highlighter-rouge">kube-scheduler</code>, <code class="highlighter-rouge">kube-controller</code>) as well as <code class="highlighter-rouge">kubelet</code> and <code class="highlighter-rouge">kubeproxy</code>. These nodes are used to help manage the Kubernetes cluster as well as where your applications (i.e. pods) can be launched.</li>
      <li><strong>worker node</strong> – On these nodes, only worker components (<code class="highlighter-rouge">kubelet</code>, <code class="highlighter-rouge">kubeproxy</code>, <code class="highlighter-rouge">nginx-proxy</code>) are launched and these nodes will only have your applications (i.e. pods) running.</li>
    </ul>
  </li>
  <li>
    <p>Finish creating your cluster by clicking <strong>Create</strong>. This process might take a few minutes to complete. Once your cluster is ready, you can view its status on the Clusters page. Once the cluster is active, you can start adding pods into your namespace.</p>
  </li>
</ol>

<h3 id="importing-kubernetes-clusters">Importing Kubernetes Clusters</h3>

<p>In Rancher v2.0, you can import an existing, external installation of Kubernetes v1.8. In this scenario, the cluster provider manages your hosts outside of Rancher.</p>

<h4 id="to-import-a-kubernetes-cluster">To Import a Kubernetes Cluster:</h4>

<ol>
  <li>Follow the instructions in the Rancher UI to import an existing Kubernetes cluster. Import the <em>kubeconfig</em> file of your existing cluster.</li>
  <li>Click <strong>Import</strong>. Once your cluster is ready, you can view its status on the Clusters page. Once the cluster is active, you can start adding pods into your namespace.</li>
</ol>

<h3 id="rancher-concepts">Rancher Concepts</h3>

<p>Rancher supports grouping resources into multiples clusters, projects and namespaces.</p>

<p>A <strong>cluster</strong> is a group of physical (or virtual) compute resources. Each project is tied to one cluster and runs its pods on the cluster’s nodes. You can share a cluster with more than one project as well as give different users access to manage the various resources of a cluster.</p>

<p>A <strong>project</strong> is a group of namespaces where workloads are defined. The pods in a project can communicate with each other over a shared managed network, and you can give different users access to manage the various resources of a project.</p>

<h3 id="adding-pods">Adding Pods</h3>

<p>After at least one cluster with nodes is created and active, you’re ready to create your first pod. You can check on your cluster status by clicking on the cluster or viewing the status on the Global view of all clusters.</p>

<h4 id="to-add-a-pod">To Add a Pod:</h4>

<ol>
  <li>Click into the <code class="highlighter-rouge">Default</code> project of a cluster.</li>
  <li>Click <strong>Deploy</strong>. The Add Pod page displays.</li>
  <li>Enter a <strong>Name</strong>, such as “first-pod.”</li>
  <li>Enter a <strong>Docker Image</strong> hosted on Docker Hub.</li>
  <li>Click <strong>Launch</strong>. This process might take a few minutes to complete. Once your pod starts running, you can view its status on the Workloads page.</li>
</ol>

<p>Now that you’ve added nodes and your first pod is up and running, you can check out the rest of our new features in Rancher v2.0.</p>

 <div class="the-content">
                                <p>Google Container Engine, or GKE for short (the K stands for Kubernetes), is Google’s offering in the space of Kubernetes runtime deployments. When used in conjunction with a couple of other components from the Google Cloud Platform, GKE provides a one-stop shop for creating your own Kubernetes environment, on which you can deploy all of the containers and pods that you wish without having to worry about managing Kubernetes masters and capacity.<span id="more-32359"></span></p>
 
<h2>Cons:</h2>
<h3>Its key features include:</h3>
<ul>
<li>Security is handled by the Google security team.</li>
<li>Compliance with HIPAA and PCI DSS is already managed.</li>
<li>Kubernetes instances are fully clustered, and will auto scale.</li>
<li>It’s based on the upstream Kubernetes project, which enables workload portability to other Kubernetes instances, whether they are another cloud provider or on-premises.</li>
</ul>

<h2>Cons:</h2>
<p>The single biggest drawback of using GKE for your Kubernetes runtime is that you are tied to Google’s Cloud Platform. If you wish to use GKE, but also want to be able to support multiple clouds, an option like <a href="http://rancher.com/rancher/" rel="noopener" target="_blank">Rancher</a> can be a help. Rancher provides a unified front end for multiple container orchestration frameworks and environments, giving you a “single pane of glass” for deploying containers wherever you need.</p>
<h2>Conclusion</h2>
<p>If you want to try Kubernetes, or currently use it and want to be able to scale without needing all the expertise in-house, GKE is a fantastic single-cloud solution for running Kubernetes. Many companies never get to the point of having a single cloud provider as a feasible option, but using a product like Rancher allows a company to leverage existing investment and expand to other cloud providers according to the dictates of customer demand.</p>

 <h2><a href="https://rancher.com/adding-custom-nodes-kubernetes-cluster-rancher-2-0-tech-preview-2/">Adding custom nodes to your Kubernetes cluster in Rancher 2.0 Tech Preview 2</a></h2>
 
 
 
 <p>The agent image can be retrieved from the <code>settings</code> endpoint in the API:</p>
<pre>AGENTIMAGE=`curl -s -H "Authorization: Bearer $APITOKEN" <a href="https://medium.com/r/?url=https%3A%2F%2F127.0.0.1%2Fv3%2Fsettings%2Fagent-image" target="_blank" rel="nofollow noopener">https://127.0.0.1/v3/settings/agent-image</a> --insecure | jq -r .value`</pre>
<p>The roles for the node, you can decide for yourself. (For this example, we’ll be using all three roles):</p>
<pre>ROLEFLAGS="--etcd --controlplane --worker"</pre>
<p>The address where the <code>rancher/server</code> container can be reached, should be self explanatory. The <code>rancher/agent</code> will connect to that endpoint.</p>
<pre>RANCHERSERVER="https://rancher_server_address"</pre>
<p>The cluster token can be retrieved from the created cluster. We saved the created clusterid in <code>CLUSTERID</code> , which we can now use to generate a token.</p>
<pre># Generate token (clusterRegistrationToken)
AGENTTOKEN=`curl -s 'https://127.0.0.1/v3/clusterregistrationtoken' -H 'content-type: application/json' -H "Authorization: Bearer $APITOKEN" --data-binary '{"type":"clusterRegistrationToken","clusterId":"'$CLUSTERID'"}' --insecure | jq -r .token`</pre>
<p>The generated CA certificate is stored in the API as well, and can be retrieved as shown below. We append <code>sha256sum</code> to generate the checksum we need to join the cluster.</p>
<pre># Retrieve CA certificate and generate checksum
CACHECKSUM=`curl -s -H "Authorization: Bearer $APITOKEN" https://127.0.0.1/v3/settings/cacerts --insecure | jq -r .value | sha256sum | awk '{ print $1 }'`</pre>
<p>All data needed to join the cluster is now available, we only need to assemble the command.</p>
<pre># Assemble the docker run command
AGENTCOMMAND="docker run -d --restart=unless-stopped -v /var/run/docker.sock:/var/run/docker.sock --net=host $AGENTIMAGE $ROLEFLAGS --server $RANCHERSERVER --token $AGENTTOKEN --ca-checksum $CACHECKSUM"</pre>
<pre># Show the command
echo $AGENTCOMMAND</pre>
  
  
  
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

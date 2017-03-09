---
ID: 22
post_title: >
  Deploying APIs built via Node.js to IBM
  Bluemix
author: admin
post_date: 2016-11-06 01:30:47
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2016/11/06/deploying-apis-built-via-node-js-to-ibm-bluemix/
published: true
---
<p>In my previous article I described how to use <a href="http://heidloff.net/article/usage-of-swagger-in-nodejs-applications-to-document-apis">Swagger to document APIs in Node.js applications</a> via a simple hello world sample. Below I demonstrate how to deploy the same sample to <a href="https://bluemix.net/">Bluemix</a> via <a href="https://www.docker.com/">Docker</a> and how to use the <a href="https://www.ng.bluemix.net/docs/services/APIManagement/index.html">API Management</a> service to enforce client ids and secrets when invoking the APIs so that API owners can monitor the usage of their APIs.<span id="more-1630"></span></p>
<p><strong>Deploying Node.js Applications to IBM Bluemix as Docker Containers</strong></p>
<p>First you need to add a Dockerfile to your project&#8217;s root directory. The <a href="https://www.ng.bluemix.net/docs/images/docker_image_ibmnode/ibmnode_starter.html">documentation</a> describes how to do this. Since I don&#8217;t need SSH I use a simpler Dockerfile.</p>
<pre class="brush: plain; title: ; notranslate">
FROM registry.ng.bluemix.net/ibmnode:latest
COPY . /node
WORKDIR /node
RUN npm install
EXPOSE 9080
CMD [&quot;node&quot;, &quot;/node/app.js&quot;]
</pre>
<p>Since the <a href="https://www.ng.bluemix.net/docs/containers/container_index.html">IBM Containers</a> (Docker hosted on Bluemix) only support specific ports I use the port 9080 instead of 10010 as in the original sample. I also had to change the port in app.js and swagger.yaml. In order to run the application locally you need to invoke the following URLs.</p>
<p><a href="http://127.0.0.1:9080/hello?name=Niklas">http://127.0.0.1:9080/hello?name=Niklas</a><br />
<a href="http://127.0.0.1:9080/swagger">http://127.0.0.1:9080/swagger</a></p>
<p>To build the Docker image and to run it locally invoke the following commands from the root directory.</p>
<pre class="brush: plain; title: ; notranslate">
docker build -t node-swagger-hello-world .
docker run --name node-swagger-hello-world -p 80:9080 -d -t node-swagger-hello-world
</pre>
<p>After this you can run the sample in our local Docker environment.</p>
<p><a href="http://dockerhost/hello?name=Niklas">http://dockerhost/hello?name=Niklas</a><br />
<a href="http://dockerhost/swagger">http://dockerhost/swagger</a></p>
<p>In order to push the image to Bluemix invoke the following commands.</p>
<pre class="brush: plain; title: ; notranslate">
cf login
cf ic login
docker tag node-swagger-hello-world registry.ng.bluemix.net/nheidloff/node-swagger-hello-world
docker push registry.ng.bluemix.net/nheidloff/node-swagger-hello-world
</pre>
<p>To run the sample on Bluemix create a Docker container group.</p>
<p><a href="http://heidloff.net/wp-content/uploads/2015/11/swaggerdockernode.png"><img src="http://heidloff.net/wp-content/uploads/2015/11/swaggerdockernode.png" alt="swaggerdockernode" width="1000" height="626" class="alignnone size-full wp-image-1626" /></a></p>
<p>After this you can run your REST API on Bluemix.</p>
<p><a href="http://node-swagger-docker-hello-world.mybluemix.net/swagger">http://node-swagger-docker-hello-world.mybluemix.net/swagger</a><br />
<a href="http://node-swagger-docker-hello-world.mybluemix.net/hello?name=Niklas">http://node-swagger-docker-hello-world.mybluemix.net/hello?name=Niklas</a></p>
<p><strong>Usage of the API Management Service</strong></p>
<p>With the <a href="https://www.ng.bluemix.net/docs/services/APIManagement/index.html">API Management</a> service in Bluemix you can manage and monitor your APIs. In the next part I describe how to enforce a client id and secret when invoking the API so that you can track which applications called which APIs. The API definition can be imported via pointing to the Swagger 2.0 definition above and additional settings can be configured in the dashboard of the API Management service.</p>
<p><a href="http://heidloff.net/wp-content/uploads/2015/11/swaggerdockernodeapi.png"><img src="http://heidloff.net/wp-content/uploads/2015/11/swaggerdockernodeapi.png" alt="swaggerdockernodeapi" width="1000" height="1714" class="alignnone size-full wp-image-1627" /></a></p>
<p>In order to invoke APIs applications developers need to register applications.</p>
<p><a href="http://heidloff.net/wp-content/uploads/2015/11/swaggerdockernodeapp.png"><img src="http://heidloff.net/wp-content/uploads/2015/11/swaggerdockernodeapp.png" alt="swaggerdockernodeapp" width="1000" height="710" class="alignnone size-full wp-image-1628" /></a></p>
<p>Applications can subscribe to plans with the APIs they are interested in and test the APIs directly from the developer portals by providing the client ids and secrets.</p>
<p><a href="http://heidloff.net/wp-content/uploads/2015/11/swaggerdockernodeportal.png"><img src="http://heidloff.net/wp-content/uploads/2015/11/swaggerdockernodeportal.png" alt="swaggerdockernodeportal" width="1000" height="1091" class="alignnone size-full wp-image-1629" /></a></p>
<p>The post <a rel="nofollow" href="http://heidloff.net/article/deploying-apis-built-via-nodejs-to-ibm-bluemix">Deploying APIs built via Node.js to IBM Bluemix</a> appeared first on <a rel="nofollow" href="http://heidloff.net/">Niklas Heidloff</a>.</p>
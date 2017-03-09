---
ID: 26
post_title: Connect to an LDAP Server using Wakanda
author: admin
post_date: 2016-11-06 01:31:54
post_excerpt: ""
layout: post
permalink: >
  http://modern-web.dev/2016/11/06/connect-to-an-ldap-server-using-wakanda/
published: true
---
<strong>Introduction:</strong>

Connecting to LDAP Server is one of the features that allow enterprise to use their LDAP directory to authenticate to their enterprise application by connecting directly to the LDAP Server like Active Directory, or using an SSO (Single Sign Out) agent, in the coming versionsÂ <a title="Wakanda Web Site" href="http://wakanda.org/" target="_blank">Wakanda</a> will support directly the SSO in the coming version as mentioned in <a title="Wakanda RoadMap" href="http://www.wakanda.org/roadmap" target="_blank">the roadmap</a>, this connector was requested by <a title="Wakanda Forum" href="http://forum.wakanda.org/index.php" target="_blank">the Wakanda community</a>, and now the latest version in DEV already provide it.

In this article we will discover how to use <a href="http://doc.wakanda.org/home2.en.html#/WAF-LDAP/WAF-LDAP.100-1069241.en.html">the LDAP connector module</a> to authenticate a user to a Wakanda business application using the company LDAP Server.

&nbsp;

<strong>Prerequisites:</strong>

<ul>
    <li>Download the latest <a href="http://www.wakanda.org/downloads/development-channel-enterprise">Wakanda Enterprise</a> which contains already the LDAP module.</li>
    <li>Active Directory Server settingsÂ : (IP address, port, base name, login and password) which will be used as the parameters for the function that will create the LDAP client in Wakanda Server.</li>
</ul>

You could download this open source LDAP explorer which could help you to browse the LDAP Directory, so you could simulate what you could get using the LDAP connector and compare the results.

&nbsp;

<strong>How to use the LDAP connector:</strong>

The LDAP connector is a Wakanda module that follows the same logic as the other CommonJS module implemented in Wakanda Server, this module is loaded first by the Wakanda Server and after providing the LDAP Server parameters it establish a connection and after that you could query (search or bind operations in this article) the LDAP directory.

<a href="http://modernweb.com/wp-content/uploads/2015/03/LDAPSchema.png"><img class="  wp-image-3473 aligncenter" src="http://modernweb.com/wp-content/uploads/2015/03/LDAPSchema-300x127.png" alt="LDAPSchema" width="383" height="162" /></a>

&nbsp;

&nbsp;

First of all, you should create an SSJS (Server Side JavaScript) module in the Wakanda solution where you will import the WAF-LDAP module and set its parameter.

Right click on the solution name and choose to create a new JavaScript File:

<a href="http://modernweb.com/wp-content/uploads/2015/03/imLDAP2.png"><img class=" size-medium wp-image-3475 aligncenter" src="http://modernweb.com/wp-content/uploads/2015/03/imLDAP2-300x131.png" alt="imLDAP2" width="300" height="131" /></a>

Now import the WAF-LDAP module in the created SSJS using the require() function :

<pre><code class="javascript">var ldap = require('waf-ldap');
</code></pre>

Now define the LDAP parameters of your Server by creating an LDAP client:

You could first define an object which contains the LDAP Server parameters and pass it to the LDAP client, the parameters passed to the <a href="http://doc.wakanda.org/home2.en.html#/WAF-LDAP/Module/ldapcreateClient.301-1069297.en.html">createClient()</a> method could be and URL or an IP address :

<pre><code class="javascript">var ldapServerParams = {
hostname: '192.168.10.38',
port: 389,
ssl: false
};
</code></pre>

Or :

<pre><code class="javascript">var ldapServerParams = {
url: 'ldap://192.168.10.38:389'
};
</code></pre>

The used parameters in our example is explicit (hostname, port and ssl), we could also add other parameters like baseDN (the base of the Distinguished Name where the search should start) define the root point where the search should start, the priority when we have the same object (user) locally in Wakanda Directory and in the remote LDAP Server and the passwordâ€¦etc.

<pre><code class="javascript">var ldapServerParams = {
hostname: '192.168.10.38',
port: 389,
ssl: false,
baseDN: 'OU=Node1,DC=LDAP1,DC=local',
password: 'LDAPPass'
};

</code></pre>

Now, we could create our LDAP client using the <a href="http://doc.wakanda.org/home2.en.html#/WAF-LDAP/Module/ldapcreateClient.301-1069297.en.html">createClient()</a> method:

<pre><code class="javascript">var client = ldap.createClient(ldapServerParams);
</code></pre>

Now, we will bind a user to the LDAP Directory using the <a href="http://doc.wakanda.org/WAF-LDAP/Client-Instances/bind.301-1069409.en.html">bind()</a> method:

<pre><code class="javascript">// define the cn and password
var ldapPass, cn;
ldapPass = "LDAPPWD";
cn = "CN=userTest,CN=Users,DC=LDAP1,DC=local";
try{
var user = client.bind(cn, ldapPass);
}
Catch(e){
console.log('Error when binding a user : '+e);
}
</code></pre>

If the given user is found in the LDAP Directory the object user will contain the user properties, if not the bind method will return null, so we could create a function that uses the same principle to authenticate a user using the LDAP Directory, the function will have the user name and the password as an entry and return trueÂ if itâ€™s authenticated or an error if not:

<pre><code class="javascript">function ldapLogin(userName, password) {
var client, cn, user;
dnSuffix = ',CN=Users,DC=LDAP1,DC=local';
client = ldap.createClient({
hostname: '192.168.10.38',
port: 389,
ssl: false
});
// build the CN for the user from the userName and the DN Suffix
cn = 'CN=' + userName + dnSuffix;
user = client.bind(cn, password);
if (user != null) {
return true;
} else {
return {
error: 1152,
errorMessage: "invalid user name or password"
}
}
}
</code></pre>

This function could be called from client side (a Wakanda Web page or a mobile application) to authenticate user via the enterprise LDAP Directory.

&nbsp;

<strong>Conclusion :</strong>

LDAP and SSO are the most used protocol to authenticate user in enterprise using an LDAP Directory like Active Directory or Lotus, having this module on Wakanda will allow enterprises to build their business applications without having the user directory in the application database and cut the development time which could be spent on organizing users on groups and defining permissions for each group which is already available in their LDAP directory.
# Apache Reverse Proxy Directives for Nextcloud in Virtualmin on Ubuntu 20.04

The following directives are an adaptation of the default directives installed by Virtualmin when creating a new Virtual Host from the Virtualmin User Interface.

These are the settings that worked to provide me with a reverse proxy setup to reach the Nextcloud server on our network.  Some key details have been redacted for security purposes.

## What it does:

When you have a web server running on port 80 and your router is pointed to that web server, sometimes you want to serve other web-based applications from other computers inside your network rather than the primary apache server on port 80/443.  So, you use a reverse proxy.  This tells apache to re-direct all requests to a particular sub-domain (dubdomain.yourdomain.com) to the machine on your network that's actually running the service or website.  So, these directives ensure that traffic ends up at the right location and the end user is served the correct information.

This was done in Ubuntu 20.04 running as a VM on an Unraid storage server through Virtualmin/Webmin.  Ubuntu 20.04 was configured to be a Webmin/Virtualmin configured server for ease of management.  Virtualmin has its own defaults that it applies to Apache which need to be considered.  

~~~
<VirtualHost 192.168.1.xxx:80>
    ServerName nextcloud.mydomain.com
    ServerAdmin <email address>

    RewriteEngine on
    RewriteCond %{SERVER_NAME} =nextcloud.jongriffith.com
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,QSA,R=permanent]
</VirtualHost>

<VirtualHost 192.168.1.xxx:443>
    ServerName nextcloud.jongriffith.com
    ServerAdmin <email address>

    <proxy *>
        AddDefaultCharset off
        Order Allow,Deny
        Allow from all
    </proxy>

    ProxyRequests     Off
    ProxyPreserveHost On

    <location />
        #RequestHeader unset Accept-Encoding
        ProxyPass https://<internal_ip>:<port>/
        ProxyPassReverse https://<interal_ip>:<port>/
        Order allow,deny
        Allow from All
    </location>

    SSLProxyEngine on
    SSLProxyVerify none 
    SSLProxyCheckPeerCN off
    SSLProxyCheckPeerName off
    SSLProxyCheckPeerExpire off
    SSLEngine on
    SSLCertificateFile /home/jongriffith.com/domains/nextcloud.jongriffith.com/ssl.cert
    SSLCertificateKeyFile /home/jongriffith.com/domains/nextcloud.jongriffith.com/ssl.key
    SSLProtocol all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
    SSLCACertificateFile /home/jongriffith.com/domains/nextcloud.jongriffith.com/ssl.ca
    IPCCommTimeout 31
</VirtualHost>
~~~
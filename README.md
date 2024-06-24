### How to use one angular build for all region or environment based deployments?

1. In environment.ts file, instead of static api endpoints make it dynamic like below:

```javascript
export const environment = {
    ....
    apiBaseUrl: "//"+ window.location.hostname+"/api/",
    logoutUrl :window.location.protocol+"//"+ window.location.hostname+"logout/public/login/index",
    siteUrl:window.location.protocol+"//"+ window.location.hostname+"/site/",
    ......

};
```


2. enable the __mod_proxy__ , __mod_proxy_http__ and __ssl__ modules

```bash
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod ssl
```

3. Add the reverse proxy configuration for HTTP:

Add the following lines to your Apache configuration file within the appropriate **<VirtualHost>** block:

```
<VirtualHost *:80>
    ServerName www.yourdomain.com

    # Proxy settings
    ProxyRequests Off

    # Proxy /api/ requests to backend server
    ProxyPass /api/ http://backend-server.com/api/
    ProxyPassReverse /api/ http://backend-server.com/api/

    # Proxy /site/ requests to another backend server
    ProxyPass /site/ http://another-backend-server.com/site/
    ProxyPassReverse /site/ http://another-backend-server.com/site/

    # Proxy /logout/ requests to yet another backend server
    ProxyPass /logout/ http://yet-another-backend-server.com/logout/
    ProxyPassReverse /logout/ http://yet-another-backend-server.com/logout/

    # Optionally, you can also configure ProxyPreserveHost
    ProxyPreserveHost On

    # Additional settings
    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>

    # Other configurations such as DocumentRoot, ErrorLog, CustomLog, etc.
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

4. Add the reverse proxy configuration for HTTPS:

```
<VirtualHost *:443>
    ServerName www.yourdomain.com

    # SSL settings
    SSLEngine On
    SSLCertificateFile /path/to/your/certificate.crt
    SSLCertificateKeyFile /path/to/your/private.key
    SSLCertificateChainFile /path/to/your/chainfile.pem # If needed

    # Proxy settings
    ProxyRequests Off

    # Proxy /api/ requests to backend server
    ProxyPass /api/ https://backend-server.com/api/
    ProxyPassReverse /api/ https://backend-server.com/api/

    # Proxy /site/ requests to another backend server
    ProxyPass /site/ https://another-backend-server.com/site/
    ProxyPassReverse /site/ https://another-backend-server.com/site/

    # Proxy /logout/ requests to yet another backend server
    ProxyPass /logout/ https://yet-another-backend-server.com/logout/
    ProxyPassReverse /logout/ https://yet-another-backend-server.com/logout/

    # Optionally, you can also configure ProxyPreserveHost
    ProxyPreserveHost On

    # Additional settings
    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>

    # Other configurations such as DocumentRoot, ErrorLog, CustomLog, etc.
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```


5. Test and restart Apache

```bash
sudo apachectl configtest
sudo systemctl restart apache2
```
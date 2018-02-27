## Apache: [mod_auth_openidc](https://github.com/pingidentity/mod_auth_openidc)

More information can be found [here](https://github.com/pingidentity/mod_auth_openidc/wiki).

The OIDC module for Apache is a very straight forward way of setting up Affiliation validation with InAcademia which can 
basically be integrated with many applications. This example assumes a script will live inside a specific directly on your 
server. The directory will be protected by the OIDC module. If InAcademia validation is succesfull the script will pick this up, set the remote user parameter and then continue to a point in the application of your preference.

### Installing mod_auth_oidc
Based on https://github.com/pingidentity/mod_auth_openidc
For most common linux distributions, pull the latest and greatest from the github release page at: 
https://github.com/pingidentity/mod_auth_openidc/releases
For debian/ubuntu this Apache module is part of the standard repositories, so install this module alternatively using:
    sudo apt install libapache2-mod-auth-openidc
Also make sure you have https/ssl configured for you domain.

*Configuring mod_auth_oidc for InAcademia*
Now configure the mod to work with InAcademia,by providing relevant configuration data to the module. 
Put these data inside the VirtualHost configuration of you server. 

For a secure configuration, you must set a decent value for CryptoPassphrase, which will be used as part of the session 
and cookie hash. Use a long random string, for example generated using
    tr -c -d ‘0123456789abcdefghijklmnopqrstuvwxyz’ </dev/urandom | dd bs=32 count=1 2>/dev/null;echo

### Configuration

```apache
Listen <port>
ServerName <server name>

LoadModule auth_openidc_module /usr/lib/apache2/modules/mod_auth_openidc.so
LoadModule ssl_module /usr/lib/apache2/modules/mod_ssl.so

<VirtualHost _default_:*>
    OIDCProviderMetadataURL <inacademia url>/.well-known/openid-configuration
    OIDCClientID <client id>
    OIDCRedirectURI https://<hostname>:<port>/your_redirect_page
    OIDCResponseType id_token
    OIDCScope "openid student"
    OIDCAuthRequestParams "claims=%7B%22id_token%22%3A%20%7B%22domain%22%3A%20null%7D%7D"

    OIDCCryptoPassphrase <secret>

    <Location /protected>
      AuthType openid-connect
      Require valid-user
    </Location>

    SSLEngine on
    SSLCertificateFile <ssl cert>
    SSLCertificateKeyFile <ssl key>
</VirtualHost>
```

*Secure location*
To trigger Affiliation validation, create a Location directive in your virtualhost section similar to the example blow.

    <Location /validate/>
    AuthType openid-connect
    Require valid-user
    LogLevel debug
    </Location>

Make sure the directory is part of the Redirect URL!

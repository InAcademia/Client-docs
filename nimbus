## Java: [Nimbus OAuth 2.0 SDK](http://connect2id.com/products/nimbus-oauth-openid-connect-sdk)

More information can be found [here](http://connect2id.com/products/nimbus-oauth-openid-connect-sdk/guides/java-cookbook-for-openid-connect-public-clients).

### Fetch provider configuration information

```java
import java.io.InputStream;
import java.net.URI;
import java.net.URL;

import com.nimbusds.openid.connect.sdk.op.OIDCProviderMetadata;

URI issuerURI = new URI(inacademiaURL);
URL providerConfigurationURL = issuerURI.resolve(
        "/.well-known/openid-configuration").toURL();
InputStream stream = providerConfigurationURL.openStream();
// Read all data from URL
String providerInfo = null;
try (java.util.Scanner s = new java.util.Scanner(stream)) {
    providerInfo = s.useDelimiter("\\A").hasNext() ? s.next() : "";
}
OIDCProviderMetadata providerMetadata = OIDCProviderMetadata
        .parse(providerInfo);
```


### Make authentication request

```java
import java.net.URI;

import com.nimbusds.oauth2.sdk.ResponseType;
import com.nimbusds.oauth2.sdk.Scope;
import com.nimbusds.openid.connect.sdk.AuthenticationRequest;
import com.nimbusds.openid.connect.sdk.ClaimsRequest;
import com.nimbusds.openid.connect.sdk.Nonce;
import com.nimbusds.openid.connect.sdk.OIDCResponseTypeValue;

Scope studentValidationScope = new Scope("openid", "student");


AuthenticationRequest.Builder authenticationRequest = new AuthenticationRequest.Builder(
        new ResponseType(OIDCResponseTypeValue.ID_TOKEN),
        studentValidationScope, clientID, redirectURI);

// Request additional claim 'domain'
ClaimsRequest claimsReq = new ClaimsRequest();
claimsReq.addIDTokenClaim("domain");

authenticationRequest.nonce(new Nonce()).claims(claimsReq)
        .endpointURI(providerMetadata.getAuthorizationEndpointURI());

URI loginURL = authenticationRequest.build().toURI();

// ... Make HTTP Redirect to loginURL
```

### Receive the authentication response

```java
StringBuilder sb = new StringBuilder();
sb.append("<html><body>");
sb.append("<form action=\"/response\" method=\"post\">");
sb.append("<input type=\"hidden\" name=\"response\" id=\"response\" value=\"\"/>");
sb.append("</form>");
sb.append("<script type=\"text/javascript\">");
sb.append("document.getElementById(\"response\").value = window.location.hash.substring(1);");
sb.append("document.forms[0].submit();");
sb.append("</script>");
sb.append("</body></html>");

// ... Make HTTP response with sb.toString()
```


### Process the authentication response server-side

```java
import java.net.URI;

import com.nimbusds.jwt.JWT;
import com.nimbusds.jwt.ReadOnlyJWTClaimsSet;
import com.nimbusds.openid.connect.sdk.AuthenticationResponseParser;
import com.nimbusds.openid.connect.sdk.AuthenticationSuccessResponse;

Map<String, String> post_data = ... // read POST data from HTTP request

URI url = new URI("http://example.com#" + post_data.get("response"));
AuthenticationResponse authResp = AuthenticationResponseParser.parse(url);
AuthenticationSuccessResponse successResponse = (AuthenticationSuccessResponse) authResp;

JWT idToken = successResponse.getIDToken();

// ... Verify the ID Token and use its claims

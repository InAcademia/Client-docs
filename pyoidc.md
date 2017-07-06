## Python 3: [pyoidc](https://github.com/rohe/pyoidc)

More information can be found [here](https://github.com/rohe/pyoidc/blob/master/doc/howto/rp.rst).

### Setup client instance

```python
from oic.oic import Client
from oic.oic.message import RegistrationResponse
from oic.utils.authn.client import CLIENT_AUTHN_METHOD

# create a client instance
client = Client(client_id=..., client_authn_method=CLIENT_AUTHN_METHOD)

# fetch the provider configuration information
client.provider_config(inacademia_url)
```

### Make authentication request

```python
from oic.oauth2 import rndstr
from oic.oic.message import Claims, ClaimsRequest
from oic.utils.http_util import Redirect


# make the authentication request
scope = "openid student" # request verification of student affiliation
claims_request = ClaimsRequest(id_token=Claims(domain=None)) # request the additional claim 'domain'
args = {
    "client_id": client.client_id,
    "response_type": "id_token",
    "redirect_uri": redirect_uri,
    "nonce": rndstr(),
    "scope": scope,
    "claims": claims_request
}
auth_req = client.construct_AuthorizationRequest(request_args=args)
login_url = auth_req.request(client.authorization_endpoint)
http_response = Redirect(login_url)
```


### Receive the authencation response

```python
from oic.utils.http_util import Response

# Send HTML page with form that POSTs the url fragment back to the server
page = """
<html><body>
<form action="/parse_response" method="post">
<input type="hidden" name="response" id="response" value=""/>
</form>
<script type="text/javascript">
document.getElementById("response").value = window.location.hash.substring(1);
document.forms[0].submit();
</script>
</body></html>
"""
http_response = Response(page)
```


### Process the authentication response server-side

```python
from oic.oic.message import AuthorizationResponse
from urllib.parse import unquote, parse_qsl

post_data = ... # read POST data from HTTP request
params = dict(parse_qsl(unquote(post_data)))["response"]
authn_resp = client.parse_response(AuthorizationResponse, params, sformat="urlencoded")
id_token = authn_resp["id_token"]

# ... Verify the ID Token and use its claims
```

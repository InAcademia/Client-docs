# Client (RP) examples

This section contains client (RP) configuration examples for implementing InAcademia in some commonly available software libraries

Note: the following code samples are not secure enough to be used in a live production environment, they are only intended to document the basic steps required to complete a transaction with the InAcademia service. Additional measures should be implemented to increase the security.

Before you can start, you need to register you service at InAcademia. You will recieve an “ClientID” and 
you must provide a RedirectURI where the response for your service will be returned.  Note that you cannot change this endpoint without re-registering.  InAcademia will ask you to prove ownership of the domain used in the RedirectURI.
More information on registration can be found here: https://inacademia.org/register/

InAcademia is a OIDC complient OP. Therfor all certified OIDC RPs shoudl work for InAcademia. A list of certified products and libraties can be found at the OpenIC Connect website: http://openid.net/developers/certified/

* Apache mod OIDC (platform independent) -> https://github.com/InAcademia/Client-docs/blob/master/Apache2%2Bmod_auth_oidc
* pyOIDC (Python) -> https://github.com/InAcademia/Client-docs/blob/master/pyoidc
* Nimbus OAuth 2.0 SDK -> https://github.com/InAcademia/Client-docs/blob/master/nimbus

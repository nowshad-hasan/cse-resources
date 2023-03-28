# Chapter 14: Implementing the Resource Server

The resource server wants to validate the access token. Three ways to it - 

- Calls the authentication server again to validate if that issued it earlier
- Authentication and resource server share same database, so resource server fetch info from db to validate that
- Authentication server uses a private key to sign, so the resource server needs to validate the signature
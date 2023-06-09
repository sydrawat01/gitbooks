---
description: Authentication for securing apps.
---

# 🔐 Authentication

## What?

Authentication is the act of verification of an end-user, who wants to access protected content on a web-app, which is generally hidden or locked away from an open user to avoid random people gaining access to protected content of the web app.

## Why?

Authentication is needed if content should be **protected,**  and is not accessible by everyone. To do this, we prevent the user from getting access to certain pages, by blocking them. Same should be done for the API requests. The request being sent to the should not succeed if the user is not logged-in.

## How?

How do we authenticate a user to gain access to our application? It's pretty straightforward, and a two-step process.

1. **Get access/permission**. \[We need to login/ create an account]
2. **Send request to protected resource.** \[with the permission attached]

### Getting Permission

The `Client (Browser)` sends a `request` (with user authentication credentials) to the `Server`. The server then replies with a `Response` which is a `YES/NO`to the `Client`. But this alone is not enough to grant access to the end-user to the protected API end-points.

![Client-server authentication process.](<../../.gitbook/assets/Screenshot 2021-05-07 at 10.57.12.png>)

### Authenticating a user

A simple YES or a NO response from a server for authentication is not secure enough. Faking a response as simple as a YES or a NO is very easy, and people can send such a fake request to the server to gain access to protected resources in our web app.

![Authenticating a user.](<../../.gitbook/assets/Screenshot 2021-05-07 at 11.21.16.png>)

To avoid this, we need to send more data along with a YES or a NO. We have two methods of doing so:

####

1. **Server-Side Sessions**\
   The server creates and stores a unique identifier on the server, and sends the same identifier to the client. The client then sends this same identifier with requests to the protected resources. The only drawback for this method is, both the server-side and client-side should be tightly coupled. If they are de-coupled, i.e, being served by different servers, then there is no tight coupling between the two.
2. **Authentication Tokens**\
   The general idea is the same with Authentication Tokens. The server **creates** but does not store, the authentication or "permission" token, and sends this token to the client. The server encrypts this token, and uses a "Key" to then hash the token to get back the credentials. This key is only known to the server \[not even the client] to decrypt the token. The client then sends this token along with the requests to the protected resources.

### Authentication Tokens

When working with "Authentication Tokens", these tokens are typically created in the "`JSON Web Token`" Format (`JWT`). These "tokens" are really just long strings which are constructed by an algorithm that encodes data into a string (with help of a private key, only known by the server).

You can learn more about JSON Web Tokens (`JWTs`) here: [https://jwt.io/](https://jwt.io/)

# Index

- [Intro](#intro)
  - [Cross-Origin Resource Sharing (CORS)](#cross-origin-resource-sharing-cors)
  - [API (Application Programming Interface)](#api-application-programming-interface)
    - [Types](#types)
  - [Middleware](#middleware)
  - [Authentication](#authentication)


# Intro

Version: 26

- Node.js is an open-source, cross-platform, server-side runtime environment that lets you run JavaScript outside the browser — typically on a server
- Built on Chrome’s V8 JavaScript engine

HTTP response status codes- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status)

HTTP method- [https://www.w3schools.com/tags/ref_httpmethods.asp](https://www.w3schools.com/tags/ref_httpmethods.asp)


## Cross-Origin Resource Sharing (CORS)

- security feature implemented by browsers that controls how resources (like APIs) on one origin (domain) can be accessed by web pages from another origin
- To protect users from malicious websites trying to access sensitive data from another site (like bank.com).
- Without CORS, any website could make requests to any server on behalf of the user
- Working
  - When your frontend sends a cross-origin HTTP request, the browser attaches an Origin header automatically.
  - The server must respond with the appropriate Access-Control-Allow-\* headers.
  - If the response is allowed, the browser gives access. Otherwise, the browser blocks the response.
- CORS headers
  - Access-Control-Allow-Origin
  - Access-Control-Allow-Methods
  - Access-Control-Allow-Headers
  - Access-Control-Allow-Credentials

Preflight Request

- CORS-related request automatically sent by the browser before the actual HTTP request, to check if the actual request is safe to send
- Working
  - Browser sends an OPTIONS request to the server.
  - The server responds with CORS headers like Access-Control-Allow-Origin, Access-Control-Allow-Methods, Access-Control-Allow-Headers
  - If the response is acceptable, the browser sends the actual request.


## API (Application Programming Interface)

set of rules and definitions that allows different software applications to communicate with each other

### Types

1\. REST API (Representational State Transfer)

- architectural style that uses HTTP methods to interact with resources
- Uses HTTP methods: GET, POST, PUT, DELETE, etc.
- Data is usually sent and received in JSON format
- Each resource is identified by a URL
- Stateless – each request is independent

2\. SOAP API (Simple Object Access Protocol)

- uses XML to send data and often runs over HTTP, SMTP, or other protocols
- Requires a WSDL (Web Services Description Language) file to describe the API
- used where reliability, security, and formal contracts are important — such as in enterprise and financial systems


## Middleware

- functions that sit between the incoming client request and the final server response
- Useful for Authentication, Authorization, Validation, Sanitization, Logging, Monitoring, Error handling, modifying requests or responses


## Authentication

JWT (JSON Web Token)

- secure, compact way to transmit information between two parties as a digitally signed token
- A JWT consists of three parts, separated by dots (.)
  - Header: Contains metadata about the token, such as the algorithm used for signing.
  - Payload: Stores the claims, i.e., data being transmitted.
  - Signature: Ensures the token's integrity and authenticity.
- Working
  - User Logs In: The client (browser) sends login credentials to the server.
  - Server Generates JWT: If credentials are valid, the server creates a JWT containing user data and signs it with a secret key.
  - Token Sent to Client: The JWT is sent back to the client and stored (usually in localStorage or a cookie).
  - Client Sends Token in Requests: For protected routes, the client includes the JWT in the Authorization header (Bearer Token).
  - Server Verifies and Responds: The server verifies the token, extracts user info, and processes the request if valid.

Access Token and Refresh Token

- Access Token- Short-lived token used to access protected resources (APIs)
- Refresh Token- Long-lived token used to get a new access token when the current one expires

OAuth

- OAuth (Open Authorization) is an authorization framework that allows a third-party app to access a user's data without getting their password.
- OAuth lets you "log in with Google/GitHub/etc." without sharing your password
- Working
  - User clicks "Log in with Google"
  - Client redirects user to Google's authorization server
  - User logs in and grants permission
  - Google redirects user back to the app with a code
  - App exchanges code for an access token (and optionally a refresh token)
  - App uses the token to access user data from Google’s API

Authentication- The process of verifying the identity of a user

Authorization- The process of giving or denying access to resources or actions based on the user's roles/permissions

Encryption

- A method of converting readable data (plaintext) into unreadable data (ciphertext) using a key
- Reversible
- Types
  - Symmetric Encryption- Encrypt and decrypt using the same key.
  - Asymmetric Encryption- Uses a public key (to encrypt) and a private key (to decrypt).
- Eg: Advanced Encryption Standard (AES), RSA, DSA

Hashing

- A method of converting data into a fixed-size string (hash)
- one-way and irreversible
- Eg: MD5, SHA-1, SHA-256, bcrypt

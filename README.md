# spring-jersey-jwt

Example of token-based authentication with Spring Security and Jersey.

## How token-based authentication works?

In a token-based authentication, the client exchanges _hard credentials_ (such as username and password) for a piece of data called _token_. Instead of sending the hard credentials in every request, the client will send the token to the server to perform authentication and authorisation.

In a few words, an authentication scheme based on tokens follow these steps:

1. The client sends their credentials (username and password) to the server.
1. The server authenticates the credentials and issues a token.
1. The server can store the previously generated token in some storage along with the user identifier.
1. The server sends the generated token in the response.
1. In each request, the client sends the token to the server.
1. The server, in each request, extracts the token from the incoming request. With the token, the server looks up the user details to perform authentication and authorisation.
    1. If the token is valid, the server accepts the request.
    1. If the token is invalid, the server refuses the request.
1. The server can provide an endpoint to refresh tokens.

## What tokens can be like?

A token can be _opaque_ which reveals no details other than the value itself (like a random string) or can be _self-contained_ (like JWT). This example uses JWT.

It's a standard method for representing claims securely between two parties, defined in the [RFC 7519][]. JWT is a self-contained token and enables you to store a user identifier, an expiration date and whatever you want (but don't store passwords) in a payload, which is a JSON encoded as Base64. The payload can be read by the client and the integrity of the token can be easily checked by verifying its signature on the server.

To find some great resources to work with JWT, have a look at [http://jwt.io][jwt.io].

JWT allows you to perform stateless authentication, that is, you won't need to persist JWT tokens if you don't need to track them. Although, by persisting the tokens, you will have the possibility of invalidating and revoking the access of them. To keep the track of JWT tokens, instead of persisting the whole token, you could persist the token identifier (the [`jti`][jti claim] claim) and some metadata (the user you issued the token for, the expiration date, etc) if you need.

Your application can provide some functionality to revoke the tokens, but always consider revoking the tokens when the users change their password. When persisting tokens, consider removing the old ones in order to prevent your database from growing indefinitely.

## How to build and run this application?

To build and run this application, follow these steps:

1. Open a command line window or terminal.
1. Navigate to the root directory of the project, where the `pom.xml` resides.
1. Compile the project: `mvn clean compile`.
1. Package the application: `mvn package`.
1. Change into the `target` directory: `cd target`
1. You should see a file with the following or a similar name: `spring-jersey-jwt-1.0.jar`.
1. Execute the JAR: `java -jar spring-jersey-jwt-1.0.jar`.
1. The application should be available at `http://localhost:8080/api`.

## What you will find in this application?

It's a REST API that that currently supports the following operations:

### Exchange hard credentials for an authentication token

```bash
curl -X POST \
  http://localhost:8080/api/auth \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "username": "<username>",
  "password": "<password>"
}'
```

### Get all users

```bash
curl -X GET \
  http://localhost:8080/api/users \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer <authentication-token>'
```

### Get a user by id

```bash
curl -X GET \
  http://localhost:8080/api/users/<user-id> \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer <authentication-token>'
```

### Get the authenticated user

```bash
curl -X GET \
  http://localhost:8080/api/users/me \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer <authentication-token>'
```

[RFC 7519]: https://tools.ietf.org/html/rfc7519
[jwt.io]: http://jwt.io/
[jti claim]: https://tools.ietf.org/html/rfc7519#section-4.1.7
jwt, jsonwebtoken, token, bearer, authentication, oauth, auth0

https://github.com/auth0/jwt-decode

# Decoding using auth0 library

Install

    npm install jwt_decode

Usage

    var token = 'eyJ0eXAiO.../// jwt token';

    var decoded = jwt_decode(token);
    console.log(decoded);

    /* prints:
     * { foo: "bar",
     *   exp: 1393286893,
     *   iat: 1393268893  }
     */
# DPoP ID Token

This is a rough draft of how to use the OAuth DPoP, RFC 9449, to get a `cnf` claim in an ID Token, binding a public key to the other claims in an ID Token. 

## Authorization Request

In addition to all the usual parameters the RP provides making an authorization request, including the `openid` scope, the RP includes:
- the `dpop_jkt` parameter per [RFC 9449 §10](https://datatracker.ietf.org/doc/html/rfc9449#name-authorization-code-binding-)  containing a JWT Thumbprint per [RFC 7683](https://datatracker.ietf.org/doc/html/rfc7638)

This is the thumbprint of the JWK that will be bound to the ID Token.

## Token Request

After receiving an authorization code, the RP creates a DPoP proof per [RFC 9449 §4](https://datatracker.ietf.org/doc/html/rfc9449#name-dpop-proof-jwts) and passes it as a DPoP header value per [RFC 9449 §5](https://datatracker.ietf.org/doc/html/rfc9449#name-dpop-access-token-request)

## ID Token

If passed an `openid` scope and a `dpop_jkt` in the authorization request, and a valid and matching DPoP proof in the `DPoP` header, the OP will add the `cnf` claim to the ID Token.

Example ID Token Payload:

```json
{
  "iss": "https://issuer.example.com",
  "aud": "Iq2",
  "nonce": "5eba3bc0-f00a-4244-8eeb-ec4a146f1cb1",
  "jti": "jti_BC508b2LXGQlDy6MkTqQN1YO_fNm",
  "sub": "7f294906-9c9e-4e1b-a690-4c9e9d4c285d",
  "email": "jane.doe@example.com",
  "email_verified": true,
  "iat": 1744255010,
  "exp": 1744255310,
  "cnf": {
      "kty": "RSA",
      "n": "0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEps2aiAFbWhM78LhWx4cbbfAAtVT86zwu1RK7aPFFxuhDR1L6tSoc_BJECPebWKRXjBZCiFV4n3oknjhMstn64tZ_2W-5JsGY4Hc5n9yBXArwl93lqt7_RN5w6Cf0h4QyQ5v-65YGjQR0_FDW2QvzqY368QQMicAtaSqzs8KJZgnYb9c7d0zgdAZHzu6qMQvRL5hajrn1n91CbOpbISD08qNLyrdkt-bFTWhAI4vMQFh6WeZu0fM4lFd2NcRwr3XPksINHaQ-G_xBniIqbw0Ls1jF44-csFCur-kEgU8awapJzKnqDKgw",
      "e": "AQAB",
      "alg": "RS256",
      "kid": "1234"
  }
}
```

## 

The OP MUST inclde `dpop_signing_alg_values_supported` in its discovery document `/.well-known/openid-configuration` with a list of supported algorithms

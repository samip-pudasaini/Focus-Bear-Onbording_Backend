**Understand how JWT-based authentication works**

**Explore how Auth0 manages user sessions and access tokens**

**Set up a simple authentication flow using Auth0 in a NestJS app**

---

### ✅ Reflection (`nestjs-auth0.md`)

- How does Auth0 handle authentication compared to traditional username/password
  auth?
- What is the role of JWT in API authentication?
- How do `jwks-rsa` and public/private key verification work in Auth0?
- How would you protect an API route so that only authenticated users can access
  it?

# Authentication in NestJS with Auth0 & JWT

## Research how Auth0 integrates with NestJS

The point of Auth0 is that Auth0 handles the identity/authentication side, which
then the access token is validated by NestJS API\

Here the different packages handle different responsibilities:

- `auth0` provides the identity platform itself: login, users, applications,
  APIs, tokens, and authentication flows. You generally don't use auth0 to
  manually authenticate every request. Auth0 has already issued the access
  token; the API needs to validate it.

- `@nestjs/jwt` package provides utilities for creating and verifying JWTs.
  NestJS documents it as a utility for JWT manipulation. Installation:
  `npm install @nestjs/jwt`

- `jwks-rsa` package retrieves public RSA keys from Auth0's JWKS endpoint.

But with Auth0, Auth0 is the issuer of the JWT. You don't need your NestJS
application to create another JWT.

Installation for this setup:

```
npm i @nestjs/passport passport passport-jwt jwks-rsa
npm i -D @types/passport-jwt
```

## How JWT-based authentication works

An Auth0 access token is a JWT containing information such as:

```json
{
  "iss": "https://YOUR-DOMAIN/",
  "sub": "auth0|123456",
  "aud": "https://YOUR-API",
  "iat": 1726890000,
  "exp": 1726893600
}
```

The three JWT parts are: `HEADER.PAYLOAD.SIGNATURE`

For example:

```
eyJhbGciOiJSUzI1NiIs...
.
eyJpc3MiOiJodHRwczov...
.
signature...
```

The payload can be decoded by anyone (it is Base64URL-encoded, not encrypted),
but decoding is not the same as validating. The API must verify that the token
was actually signed by Auth0 and that important claims such as the issuer,
audience and expiration are valid.

## How the API validates the token

```
Client logs in via Auth0        -> Auth0 issues a signed access token
Client calls API                -> Authorization: Bearer <token>
API reads `kid` from the header -> fetches the matching public key from JWKS
API verifies:
  signature (RS256, public key)  fail -> 401
  iss (my Auth0 tenant)          fail -> 401
  aud (my API identifier)        fail -> 401
  exp (not expired)              fail -> 401
Valid -> handler runs, claims available as req.user
```

## How Auth0 manages sessions and access tokens

- Auth0 session: when a user logs in through Universal Login, Auth0 sets a
  session cookie on the Auth0 domain. This is what gives single sign-on: a
  second app can get tokens without asking for the password again.
- Access token: short-lived JWT sent to the API. Its lifetime is set per API in
  the Auth0 dashboard, and shorter is safer. ID token: tells the client who the
  user is. It is not sent to the API.
- Refresh token: lets the client get a new access token without logging in
  again. Enable refresh token rotation so a stolen token has limited use.
- Logout: clearing the app's tokens is not enough. The Auth0 session must also
  be cleared (Auth0 logout endpoint), or the user is silently logged back in.
- Flow: single-page apps use Authorization Code with PKCE, so no client secret
  is exposed in the browser.

## Set up a simple authentication flow in NestJS

**Auth0** Applications -> Create Application (e.g. a SPA or Regular Web App) for
the client. APIs -> Create API. The Identifier (e.g. https://my-api) is the
audience.

**Environment**:

```
AUTH0_DOMAIN=YOUR_TENANT.au.auth0.com
AUTH0_AUDIENCE=https://my-api
```

**Strategy**(verifies the token)

```ts
// jwt.strategy.ts

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    const domain = process.env.AUTH0_DOMAIN!;
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      secretOrKeyProvider: passportJwtSecret({
        cache: true,
        rateLimit: true,
        jwksRequestsPerMinute: 5,
        jwksUri: `https://${domain}/.well-known/jwks.json`,
      }),
      audience: process.env.AUTH0_AUDIENCE,
      issuer: `https://${domain}/`,
      algorithms: ["RS256"],
    });
  }

  validate(payload: { sub: string }) {
    return { userId: payload.sub }; // becomes req.user
  }
}
```

**Module**

```ts
@Module({
  imports: [PassportModule.register({ defaultStrategy: "jwt" })],
  providers: [JwtStrategy],
})
export class AuthModule {}
```

**Protected route**

```ts
@Controller()
export class AppController {
  @Get("public")
  publicRoute() {
    return { message: "Anyone can see this" };
  }

  @Get("private")
  @UseGuards(AuthGuard("jwt"))
  privateRoute(@Req() req: { user: { userId: string } }) {
    return { message: "Authenticated", userId: req.user.userId };
  }
}
```

**Test using**

```
curl http://localhost:3000/private                                   # 401
curl -H "Authorization: Bearer <ACCESS_TOKEN>" http://localhost:3000/private  # 200
```

## Reflection

### How does Auth0 handle authentication compared to traditional username/password auth?

In traditional auth, my own app stores password hashes, runs the login endpoint,
issues sessions or tokens, and has to build password reset, MFA, brute-force
protection and social login itself. Every one of those is a place to make a
security mistake.

With Auth0, authentication is delegated. The user logs in on Auth0's Universal
Login page, so my app never sees or stores the password. Auth0 handles MFA,
social logins, breach and brute-force protection, and issues signed tokens using
standard OAuth2/OpenID Connect. My API only validates tokens.

### What is the role of JWT in API authentication?

The JWT is the proof of identity the client sends with each request. It is
self-contained and signed, so the API can verify who the user is (`sub`), who
issued the token (`iss`), who it is for (`aud`), and when it expires (`exp`)
without a database lookup or a server-side session. That makes the API stateless
and easy to scale. The trade-off is that a token can't be revoked before it
expires, so lifetimes should be short.

### How do `jwks-rsa` and public/private key verification work in Auth0?

Auth0 signs each token with its **private key**, which never leaves Auth0. It
publishes the matching **public keys** at
`https://YOUR_DOMAIN/.well-known/jwks.json`. The token header contains a `kid`
(key ID). `jwks-rsa` fetches the JWKS, finds the key with that `kid`, and hands
the public key to `passport-jwt` to verify the signature. It caches the keys and
rate-limits requests, so Auth0 isn't called on every request.

Because verification only needs the public key, my API can check tokens but can
never create them, and Auth0 can rotate keys without any code change.

### How would you protect an API route so that only authenticated users can access it?

Add `@UseGuards(AuthGuard('jwt'))` to the route (or controller). The guard runs
the JWT strategy, which extracts the Bearer token and verifies signature,
issuer, audience and expiry. If anything fails, the request gets a 401 and never
reaches the handler. If it passes, `validate()` builds `req.user` and the
handler runs. To protect every route by default, register the guard as an
`APP_GUARD` and mark open routes with a `@Public()` decorator.


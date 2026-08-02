---
name: Authenticate with Sign-In with Ethereum (Locksmith)
description: Log a wallet into the Locksmith API using a SIWE (EIP-4361) signed message and manage the issued tokens.
api: openapi/unlock-protocol-locksmith-openapi.yml
operations: [nonce, login, user, logout, revoke]
generated: '2026-07-21'
method: generated
---

# Authenticate with Sign-In with Ethereum

Locksmith protects user endpoints with a bearer JWT issued through a Sign-In with Ethereum
(EIP-4361) flow. There is no OAuth; do not look for an authorize/token endpoint.

1. `nonce` — GET `/v2/auth/nonce` to fetch a fresh nonce.
2. Build a SIWE message containing the nonce, the user's wallet address, your domain/uri, and
   the chainId (use `LocksmithService.createSiweMessage` from `@unlock-protocol/unlock-js` if in
   JavaScript). Have the wallet sign it.
3. `login` — POST `/v2/auth/login` with the message + signature. The response contains a
   short-lived `accessToken` (JWT) and the `walletAddress`, plus a refresh token.
4. Send `Authorization: Bearer <accessToken>` on subsequent requests. Confirm identity with
   `user` (GET `/v2/auth/user`).
5. When the access token expires, use the refresh token to obtain a new one; `revoke` cancels a
   refresh token and `logout` ends the session.

Rules:
- Application-level access uses the separate `api-key` **query parameter** scheme (see
  `authentication/unlock-protocol-authentication.yml`) — never mix it with user bearer tokens.
- No idempotency keys exist; treat non-GET retries with care (see
  `conventions/unlock-protocol-conventions.yml`).
- Errors are plain JSON with HTTP status codes: expect 401 (NotAuthenticated) on missing/expired
  tokens (see `errors/unlock-protocol-problem-types.yml`).

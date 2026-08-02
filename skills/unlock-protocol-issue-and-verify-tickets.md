---
name: Issue and verify event tickets
description: Use Locksmith to email, render, and check in NFT event tickets (keys) with QR codes and verifiers.
api: openapi/unlock-protocol-locksmith-openapi.yml
operations: [getEventTicket, emailTicket, ticketQRCode, ticketVerificationUrl, checkEventTicket, eventVerifiers, addEventVerifier, generateAppleWalletPass, generateGoogleWalletPass]
generated: '2026-07-21'
method: generated
---

# Issue and verify event tickets

Keys (membership NFTs) double as event tickets identified by `(network, lockAddress, keyId)`;
events are identified by a `slug`.

1. Authorize as a lock manager or verifier (SIWE bearer token — see the authenticate skill).
2. Delivery: `emailTicket` sends the ticket by email; `ticketQRCode` returns the QR code image;
   `ticketVerificationUrl` returns the signed verification URL embedded in the QR;
   `generateAppleWalletPass` / `generateGoogleWalletPass` produce wallet passes.
3. Inspect a ticket with `getEventTicket` (GET
   `/v2/api/ticket/{slug}/{network}/lock/{lockAddress}/key/{keyId}`).
4. Check-in: authorize the door staff by adding them as verifiers with `addEventVerifier`
   (list with `eventVerifiers`), then mark attendance with `checkEventTicket` (PUT
   `/v2/api/ticket/{slug}/{network}/lock/{lockAddress}/key/{keyId}/check`).

Rules:
- `checkTicket` (non-slug variant) and the `/v2/api/verifier/...` operations (`verifiers`,
  `createVerifier`, `deleteVerifier`, `verifier`) are **deprecated** — use the event-scoped
  equivalents (see `lifecycle/unlock-protocol-lifecycle.yml`).
- Check-in is a state change with no idempotency contract; a 409/4xx on re-check means the
  ticket was already checked in — do not retry blindly.

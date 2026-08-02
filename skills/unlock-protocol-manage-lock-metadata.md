---
name: Manage lock, key, and user metadata
description: Store and read off-chain metadata for locks, keys, and members via the Locksmith API.
api: openapi/unlock-protocol-locksmith-openapi.yml
operations: [lockMetadata, updateLockMetadata, keyMetadata, updateKeyMetadata, getUserMetadata, updateUserMetadata, updateUsersMetadata, keysByPage, exportKeys, getExportedKeys]
generated: '2026-07-21'
method: generated
---

# Manage lock, key, and user metadata

Locksmith stores the off-chain metadata for locks (NFT collection metadata), keys (per-token
attributes), and users (per-member data such as emails), scoped by `(network, lockAddress)`.

1. Authorize as the lock manager (SIWE bearer token).
2. Lock level: `lockMetadata` reads, `updateLockMetadata` writes (PUT
   `/v2/api/metadata/{network}/locks/{lockAddress}`).
3. Key level: `keyMetadata` reads, `updateKeyMetadata` writes for one `keyId`.
4. User level: `getUserMetadata` / `updateUserMetadata` per `userAddress`; bulk-write with
   `updateUsersMetadata` (PUT `/v2/api/metadata/users`).
5. Membership roster: page through keys with `keysByPage`; for a full export use `exportKeys`
   to start the job (returns 202 with a `jobId`), then poll `getExportedKeys`.

Rules:
- The bare `keys` listing is **deprecated** in favor of `keysByPage`; pagination is
  page/offset style (`page`, `max`/`pageSize` params — see
  `conventions/unlock-protocol-conventions.yml`).
- Metadata reads of protected fields require lock-manager authorization; expect 401/403
  otherwise (see `errors/unlock-protocol-problem-types.yml`).

---
name: gainsight-px-sync-account
description: Create or update a Gainsight PX account record and confirm the write, using the published PX REST contract.
api: Gainsight PX REST API
spec: openapi/gainsight-px-rest-api-openapi.yml
base_url: https://api.aptrinsic.com/v1
operations:
  - getAccountsUsingGET
  - getAccountUsingGET
  - createAccountUsingPOST
  - updateAccountUsingPUT_1
  - deleteAccountUsingDELETE
---

# Sync an account into Gainsight PX

## Before you start

- Authentication is an API key in the `X-APTRINSIC-API-KEY` request header. A **Write**-permission key is required for anything but the reads.
- Pick the base for the subscription's data center: `https://api.aptrinsic.com/v1` (US), `https://api-eu.aptrinsic.com/v1` (EU), `https://api-us2.aptrinsic.com/v1` (US2). Calling the wrong region returns 404, not a redirect.

## Steps

1. **Check whether the account already exists.** `getAccountUsingGET` — `GET /v1/accounts/{accountId}`. A `404 Account not found` means it does not.

   Do not skip this. The contract declares **no idempotency mechanism** (`conventions/gainsight-conventions.yml`, `idempotency.coverage: none`), so a retried create after a timeout can produce a duplicate. Read-before-write is the only protection available.

2. **Create it if absent.** `createAccountUsingPOST` — `POST /v1/accounts`. A `400 Bad request, possible duplicate` is Gainsight's server-side duplicate detection firing; treat it as "already exists" and go to step 3 rather than retrying the create.

3. **Update it if present.** `updateAccountUsingPUT_1` — `PUT /v1/accounts/{accountId}`. There is also `updateAccountUsingPUT` (`PUT /v1/accounts/update`), which takes the `accountId` in the body instead of the path; prefer the path form so the target is visible in the request line.

4. **Confirm.** Re-read with `getAccountUsingGET` and compare the fields you set. The `Account` definition carries 24 fields; only assert on the ones you wrote.

## Error handling

| Status | Meaning | Do |
|---|---|---|
| 400 | Bad request | Fix the body against the `Account` schema. Do not retry unchanged. |
| 400 | Bad request, possible duplicate | Treat as exists; switch to update. |
| 401 | Unauthorized or bad API Key | Check the header name and the key's permission flag. |
| 403 | Access denied | The key lacks Write permission. |
| 404 | Account not found | Expected on the existence check. |
| 429 | Rate limit exceeded | Back off. **No `Retry-After` header is returned** — use your own schedule against ~200 req/s and 1,000,000/day. |

## Reversal

`deleteAccountUsingDELETE` removes the account. No restore operation and no retention window is published — treat the delete as permanent.

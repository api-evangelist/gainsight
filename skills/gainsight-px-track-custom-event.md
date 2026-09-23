---
name: gainsight-px-track-custom-event
description: Write a custom product event into Gainsight PX and read the history back, using the published PX REST contract.
api: Gainsight PX REST API
spec: openapi/gainsight-px-rest-api-openapi.yml
base_url: https://api.aptrinsic.com/v1
operations:
  - createCustomEventUsingPOST
  - getCustomEventsUsingGET
  - getUserUsingGET
---

# Track a custom event in Gainsight PX

## Before you start

- `X-APTRINSIC-API-KEY` header with **Write** permission.
- The event's subject must already exist in PX. Events written against an unknown user are not an error you can see from the response.

## Steps

1. **Confirm the subject.** `getUserUsingGET` — `GET /v1/users/{identifyId}`. A `404 User not found by id` means the event has nobody to attach to; create the user first (`createUserUsingPOST`) or abort.

2. **Write the event.** `createCustomEventUsingPOST` — `POST /v1/events/custom`. The `CustomEvent` definition is the request shape; `CustomEventMetadata` describes the custom attributes a subscription has configured.

3. **Read it back.** `getCustomEventsUsingGET` — `GET /v1/events/custom`. The response is a `CustomEventsPage` envelope — page through it rather than assuming one call returns everything.

## The rule that matters here

**Event ingestion is append-only.** The contract exposes no delete for a custom event once written, and there is no idempotency key. A retry after a network timeout writes a second event, and you cannot remove either one. Before retrying a `createCustomEventUsingPOST` that returned no response, read `getCustomEventsUsingGET` and check whether the first attempt landed.

Batch writes surface partial failure through the `CustomEventsBatchError` definition — the one modelled error schema in this contract. Read it rather than branching on the status code alone.

## Error handling

`401` bad key · `403` key lacks Write · `429` rate limit with no `Retry-After` · `400` schema mismatch. Full table in `errors/gainsight-problem-types.yml`.

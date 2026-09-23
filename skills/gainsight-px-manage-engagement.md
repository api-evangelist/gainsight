---
name: gainsight-px-manage-engagement
description: Inspect a Gainsight PX in-app engagement, move it between environments and states, and understand what can be taken back.
api: Gainsight PX REST API
spec: openapi/gainsight-px-rest-api-openapi.yml
base_url: https://api.aptrinsic.com/v1
operations:
  - getEngagementsUsingGET
  - getEngagementUsingGET
  - getEngagementsExtendedUsingGET
  - changeEngagementStateUsingPUT
  - changeEngagementEnvUsingPUT
  - changeEngagementEnvsUsingPUT
  - deleteEngagementUsingDELETE
---

# Manage a Gainsight PX engagement

## Before you start

- `X-APTRINSIC-API-KEY` header. Changing state or environment needs **Write**; pushing an engagement live in production needs the separate **Production Launch** permission. The contract declares one flat apiKey scheme and says nothing about this — it is a key-administration setting, and a call that fails on it returns `403 Access denied`.

## Steps

1. **List engagements.** `getEngagementsUsingGET` — `GET /v1/engagement`. Use `getEngagementsExtendedUsingGET` (`GET /v1/engagement/extended`) when you also need the custom events each engagement's rules reference.

2. **Read one.** `getEngagementUsingGET` — `GET /v1/engagement/{engagementId}`. Record its current state and environment before you change anything; this is your rollback value.

3. **Move the environment.** `changeEngagementEnvUsingPUT` (`PUT /v1/engagement/env`) for one, `changeEngagementEnvsUsingPUT` (`PUT /v1/engagement/envs`) for several.

4. **Change the state.** `changeEngagementStateUsingPUT` — `PUT /v1/engagement/state`.

## Reversibility

This is the one genuinely two-way write in the PX contract: engagement state and environment are settable fields, so step 4 can be undone by calling the same operation with the value you recorded in step 2. No time window applies.

`deleteEngagementUsingDELETE` is not reversible — there is no restore and no published retention window. Do not use it as an "off switch"; set the state instead.

## Error handling

`401` bad key · `403` key lacks Write or Production Launch · `404` engagement not found · `429` rate limit, no `Retry-After` header.

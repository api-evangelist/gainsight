---
name: gainsight-cs-mcp-connect
description: Connect an MCP client to a Gainsight CS tenant's MCP server, discovering the authorization server from the challenge rather than hard-coding it.
api: Gainsight CS MCP Server
manifest: mcp/gainsight-mcp.yml
endpoint: https://<tenant>.gainsightcloud.com/v1/ds-mcp/mcp
---

# Connect to the Gainsight CS MCP server

## What this server is

A first-party **remote** MCP server. An MCP client POSTs JSON-RPC to
`https://<tenant>.gainsightcloud.com/v1/ds-mcp/mcp`, where `<tenant>` is the
customer's own Gainsight base host. There is no shared or public endpoint —
the URL is per tenant by design.

Gainsight also ships a **local stdio** server in `@gainsight/gs-admin-cli`
(`npx -y @gainsight/gs-admin-cli`). That is a different product surface —
admin operations, JO Programs, Data Management — and it needs a human to
install and run it before any agent sees it.

## Steps

1. **POST `tools/list` unauthenticated.** You will get `401` with body
   `{"result":false,"errorCode":"GS_APIG_2401","errorDesc":"Unauthorized",...}`.
   This is the expected first move, not a failure.

   A `404` with `errorCode: GS_APIG_1000` instead means the route does not exist
   on that host — you have the wrong tenant base, not the wrong credentials.

2. **Discover the authorization server.** Fetch
   `https://<tenant>.gainsightcloud.com/.well-known/oauth-protected-resource/v1/ds-mcp/mcp`.
   It is served anonymously and returns RFC 9728 metadata naming the MCP
   resource, its `authorization_servers[]`, `scopes_supported` (`read`,
   `read_write`) and `bearer_methods_supported` (`header`).

3. **Fetch the authorization-server metadata.** `GET
   /.well-known/oauth-authorization-server` on the same host — RFC 8414, also
   anonymous. It gives `authorization_endpoint` (`/v1/authorize`),
   `token_endpoint` (`/v1/users/oauth/access/token`),
   `code_challenge_methods_supported: ["S256"]` and
   `scopes_supported: ["read","read_write","offline_access"]`.

4. **Run authorization code + PKCE.** S256 is required; PKCE must be enabled on
   the Gainsight CS side by an admin. Request `offline_access` only if you need
   a refresh token.

5. **Retry `tools/list` with `Authorization: Bearer <token>`.**

## What you can and cannot do once connected

**Read:** companies, relationships, CTAs, CTA tasks, Success Plans, Timeline
activities, scorecards, reports, portfolio data, object metadata, templates,
picklist values, contact search.

**Write:** create/update CTAs and their tasks, create/update Success Plans,
create Timeline entries at Company, Relationship, Success Plan and CTA level.

**Cannot:** user management, connector configuration, Customer Goals, Rules
Engine changes, schema or custom field creation, C360 layout, health-score
configuration, bulk batch operations, notification management, **any delete**,
and Scorecard-level Timeline entries.

## The thing to hold onto before you write

There are **no delete operations on this surface**. A CTA, task, Success Plan
or Timeline entry you create through MCP cannot be removed through MCP — a
human has to do it in the Gainsight UI. Combined with the absence of any
idempotency key, a retried create is a permanent duplicate. Read first, write
once, and do not retry a create whose outcome you have not checked.

Permissions are the authenticated user's own, and field-level security and
mandatory fields are enforced. MCP calls count against the tenant API limit
(100/min, 100,000/day).

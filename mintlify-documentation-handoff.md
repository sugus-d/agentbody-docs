# AgentBody Mintlify Documentation Handoff

## Purpose

This document assigns ownership and operating rules for the public AgentBody developer documentation site. It is a handoff document, not the documentation site itself.

The intended public surfaces are:

| Surface | Purpose | Owner |
| --- | --- | --- |
| `agentbody.io` | Marketing site, authentication, and Console | AgentBody web team |
| `docs.agentbody.io` | Human-readable developer documentation and API reference | Documentation owner |
| `api.agentbody.io` | Production AgentBody Gateway API | Gateway team |

## Architecture

```text
Gateway repository
  owns API implementation and canonical OpenAPI contract
        |
        | automated, validated export
        v
sugus-d/docs repository
  owns Mintlify configuration and human documentation
        |
        | push or merge to main
        v
Mintlify
  publishes docs.agentbody.io

agentbody repository
  optionally links visitors to docs.agentbody.io
```

The documentation repository must never become a second hand-maintained definition of the API.

## Current Provisioning State

- A Mintlify project named `agentbody` exists.
- Mintlify is connected to the `sugus-d/docs` repository on branch `main`.
- The default Mintlify Starter Kit is deployed to the Mintlify-provided temporary domain.
- The production custom domain `docs.agentbody.io` has not yet been configured.
- The initial repository configuration still needs to be replaced with AgentBody-specific content and navigation.

## Ownership Boundaries

### Gateway team

The Gateway team owns:

- API implementation and production behavior.
- The canonical OpenAPI contract, including request/response schemas, security requirements, error responses, pagination, rate-limit headers, and examples.
- Any change to an API operation's path, method, authentication, parameter, response, or error behavior.
- The automated process that exports the approved OpenAPI artifact to the documentation repository.

Every API-changing pull request must update and validate the canonical OpenAPI contract in the same change.

### Documentation owner

The documentation owner owns the `sugus-d/docs` repository:

- `docs.json` site configuration, navigation, branding, and custom domain settings.
- Narrative pages such as introduction, authentication, quickstart, retry guidance, billing concepts, and FAQs.
- Cross-links and examples that explain the API without redefining its formal schema.
- Review of Mintlify previews and production documentation quality.

The documentation owner must not manually edit the OpenAPI artifact copied from Gateway. Changes to API contracts go to the Gateway team.

### AgentBody web team

The web team owns the optional `Documentation` link from `agentbody.io` to `https://docs.agentbody.io`. The web application does not build or deploy the Mintlify site.

## Repository Layout

The documentation owner should organize `sugus-d/docs` along these lines:

```text
docs.json
index.mdx
quickstart.mdx
authentication.mdx
errors-and-retries.mdx
billing-and-usage.mdx
api-reference/
  openapi.json              # managed by Gateway export; do not edit manually
assets/
```

The exact Mintlify OpenAPI configuration should reference the managed `api-reference/openapi.json` artifact. Keep narrative MDX and generated/managed API material visibly separate.

## OpenAPI Synchronization Contract

### Required outcome

A successful Gateway release or contract update must make the corresponding OpenAPI artifact available in `sugus-d/docs` through an automated, auditable change. Mintlify then deploys the updated API reference from the documentation repository's `main` branch.

### Recommended workflow

1. Gateway engineer changes API behavior and canonical OpenAPI in one pull request.
2. Gateway CI validates the contract against the API implementation and tests.
3. A Gateway-controlled workflow opens a pull request in `sugus-d/docs` containing the exported `api-reference/openapi.json` change.
4. The documentation owner reviews the generated API diff and confirms any associated narrative updates.
5. Merge the documentation pull request into `main`.
6. Mintlify automatically builds and publishes the update.

This pull-request handoff is preferred over silently force-pushing the documentation branch: it preserves an audit trail and lets the documentation owner review breaking changes.

### Temporary fallback

If automation is not yet available, a Gateway owner may manually copy the canonical artifact into the managed OpenAPI path through a documentation-repository pull request. The copied file is still generated/managed output and must not be edited independently.

## Content Standards

Every public page must:

- Describe only currently supported public behavior.
- Use `https://api.agentbody.io` as the API base URL.
- Use placeholders such as `<AGENTBODY_API_KEY>` in examples.
- Explain retries and status-code handling based on the canonical contract.
- Avoid hardcoded per-tool credit prices; pricing is Gateway-managed and can change.
- Avoid upstream provider URLs, credentials, raw upstream errors, internal task IDs, and internal implementation details.

Never publish a real API key, token, database value, webhook secret, or environment variable in Markdown, configuration, screenshots, issues, or pull requests.

## Publishing Process

For every documentation-only change:

1. Create a branch from `main`, for example `docs/authentication-guide`.
2. Make the change in `sugus-d/docs`.
3. Preview locally with the Mintlify CLI when practical.
4. Open a pull request and review the Mintlify preview.
5. Merge only after the preview is correct.
6. Confirm the Mintlify production build succeeds after the `main` update.

For changes that combine Gateway and documentation work, use two linked pull requests: one in Gateway for the contract and behavior, and one in `sugus-d/docs` for the exported contract and explanatory content.

## Custom Domain Rollout

Configure the custom domain only after the AgentBody landing page, navigation, and first API reference pass review.

1. In Mintlify, add `docs.agentbody.io` in Domain setup.
2. Add exactly the DNS record Mintlify provides in the DNS provider for `agentbody.io`.
3. Wait for Mintlify verification and HTTPS issuance.
4. Verify `https://docs.agentbody.io` loads the intended site.
5. Confirm `agentbody.io` continues to route to Vercel and `api.agentbody.io` continues to route to Gateway.
6. Add or update the external Documentation link on the marketing site.

Do not alter the DNS record for `api.agentbody.io`.

## Launch Acceptance Checklist

- [ ] `docs.json` identifies the site as AgentBody, with no leftover Mintlify starter links.
- [ ] Navigation includes Introduction, Quickstart, Authentication, Error handling, and API Reference.
- [ ] API Reference renders from the Gateway-managed OpenAPI artifact.
- [ ] Examples use placeholders and the production API base URL.
- [ ] No API key, secret, internal endpoint, upstream provider detail, or fixed tool price is exposed.
- [ ] Mintlify preview succeeds for a documentation pull request.
- [ ] Mintlify production build succeeds from `main`.
- [ ] `docs.agentbody.io` has valid HTTPS and serves the intended documentation.
- [ ] `agentbody.io` and `api.agentbody.io` are unaffected.

## Escalation Rules

- Missing or incorrect endpoint schema: escalate to the Gateway team; do not patch it only in documentation.
- Broken documentation layout, navigation, links, or prose: documentation owner fixes it in `sugus-d/docs`.
- DNS or custom-domain verification failure: escalate to the person who administers the `agentbody.io` DNS zone.
- API key exposure: revoke the affected key immediately through the appropriate control plane, remove it from public history where possible, and notify the security owner.


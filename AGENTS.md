# AgentBody documentation project instructions

## About this project

- This repository publishes the AgentBody developer documentation site through Mintlify.
- Documentation pages use MDX with YAML frontmatter.
- Site configuration lives in `docs.json`.
- The public documentation domain is `docs.agentbody.io` after custom-domain rollout.
- The production Gateway API base URL is `https://api.agentbody.io`.

## Ownership boundaries

- The Gateway team owns the API implementation and canonical OpenAPI contract.
- The Gateway team exports the approved contract to `api-reference/openapi.json` through an auditable pull request.
- Do not manually edit `api-reference/openapi.json`.
- Documentation owns narrative MDX, navigation, branding, and cross-links.
- Do not document unverified endpoint paths, methods, schemas, or error behavior in narrative pages.

## Writing style

- Use active voice and second person.
- Keep sentences concise and use sentence case for headings.
- Use bold for UI labels and code formatting for commands, paths, and identifiers.
- Use `https://api.agentbody.io` in API examples.
- Use placeholders such as `<AGENTBODY_API_KEY>`; never include real credentials.

## Content boundaries

- Document only currently supported public behavior.
- Do not expose internal endpoints, upstream provider URLs, raw upstream errors, internal task IDs, secrets, or environment values.
- Do not publish fixed per-tool credit prices.
- Retry, status-code, pagination, and rate-limit guidance must align with the canonical OpenAPI contract.

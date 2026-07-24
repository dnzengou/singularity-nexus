# Security Policy

## Supported Versions

Only the latest `main` branch of Singularity Nexus receives security updates.

| Version | Supported |
|---------|-----------|
| 1.0.x   | ✅ |
| < 1.0   | ❌ |

## Reporting a Vulnerability

Please report suspected vulnerabilities privately via GitHub's [Security Advisories](https://github.com/dnzengou/singularity-nexus/security/advisories/new) tab on this repo, or by email to the maintainer listed on the profile.

Do **not** open a public issue for security matters.

### Response

- Acknowledgement within 5 business days
- Fix or mitigation within 30 days for high-severity findings
- Credit in the changelog if desired

## Scope

In-scope:
- XSS / injection in `index.html`
- CSP bypasses
- localStorage tamper vectors that escalate privilege
- Payment flow gaps (Stripe URL substitution, `?activated=1` misuse)

Out of scope:
- Vulnerabilities in Tailwind CDN or Three.js CDN (report upstream)
- Social engineering / physical attacks
- Findings requiring privileged local access

## Known limits by design (honor-system caveats)

Singularity Nexus ships as a static single-file HTML app with no backend. This has security implications that are **not defects but design trade-offs**:

- **Pro activation is client-side only.** The `?activated=1` URL param sets a `localStorage` flag that the app trusts. A user with DevTools can flip the flag manually and unlock premium features without paying. This is the standard trade-off of a serverless freemium checkout; we ship it honestly rather than pretending it's a licence check.
- **EvoMetaClaw session seal is FNV-1a, not cryptographic.** The `checksum()` function is a fast non-cryptographic hash. It provides *tamper-evidence for casual inspection*, not tamper-resistance against a motivated attacker. A real audit trail requires server-side signing (Ed25519 or equivalent).
- **Lead-capture webhook is user-configured.** The gear-icon config panel lets the site operator paste an arbitrary POST endpoint. If misconfigured to a third party, lead data leaks there. We do not validate the webhook target.

The **enforceable-licensing path** (Stripe webhook + signed unlock token verified server-side) is scoped as a Phase 2 item in [BLUEPRINT.md](../BLUEPRINT.md). Adding a backend does not require a frontend rewrite.

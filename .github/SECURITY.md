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

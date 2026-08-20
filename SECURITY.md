# Security Policy

CME is a capability declaration standard. Security reports are especially relevant when a provider can under-declare or misrepresent authority-relevant Effects, bypass dynamic resolution, create stale time-of-check/time-of-use decisions, or blur organizational authority with platform security.

## Reporting a vulnerability

Please avoid publishing exploit details for an unpatched vulnerability in a public issue.

If GitHub Private Vulnerability Reporting is enabled for this repository, use that mechanism. Otherwise, contact the repository owner through GitHub and request a private reporting channel before sharing sensitive reproduction details.

Non-sensitive specification ambiguities, conservative-policy questions, and hardening proposals may be filed publicly.

## Security boundary

CME does not grant permission and must not be treated as a replacement for:

- MCP authorization;
- OAuth or credential controls;
- host approval mechanisms;
- sandbox boundaries;
- operating-system privilege checks;
- Windows UAC or equivalent platform confirmation;
- application-specific security controls.

A provider manifest is an assertion made by that provider. Consumers remain responsible for provider trust policy and conservative handling of unknown or unverified capability information.

# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in this repository, **please do not open a public GitHub issue**.

Instead, report it privately to the Tenstorrent security team:

- **Email**: security@tenstorrent.com
- **Subject**: `[SECURITY] tt-cluster-descriptors — <brief description>`

Please include:
- A description of the vulnerability
- Steps to reproduce
- Potential impact assessment
- Any suggested mitigations (optional)

We will acknowledge receipt within 3 business days and aim to provide a resolution timeline within 10 business days.

## Supported Versions

This repository contains static configuration files. Security fixes are applied to the `main` branch only.

## Scope

This repository does not contain executable code. Security concerns are most likely to relate to:
- Incorrect hardware topology descriptors that could cause misconfiguration
- Supply chain / integrity concerns about descriptor files consumed by CI

## Disclosure Policy

We follow responsible disclosure. Once a fix is merged, we will acknowledge the reporter (with permission) in the release notes or commit message.

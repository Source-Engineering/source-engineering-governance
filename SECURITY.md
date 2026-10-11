# Security policy

The Source Engineering discipline takes the security of its specifications, reference implementations, and tools seriously. This document describes how to report a vulnerability and what to expect.

## Supported versions

The discipline's reference implementations, conformance suite, and tools are versioned. Security fixes are applied to the latest minor release and the previous minor release. Older releases do not receive security updates.

## Reporting a vulnerability

If you discover a security vulnerability in any Source Engineering artefact, report it by emailing the founding author. Do not open a public issue. Include:

- A description of the vulnerability
- Steps to reproduce
- The affected artefact and version
- Your assessment of the impact

The founding author will acknowledge receipt within 3 business days and will provide an initial assessment within 10 business days.

## Disclosure policy

The discipline follows a coordinated disclosure policy. Once a fix is available, the founding author will:

1. Release the fix in a patch release.
2. Publish a security advisory on the affected repo.
3. Credit the reporter (unless the reporter prefers anonymity).

The discipline aims to disclose vulnerabilities within 90 days of the initial report.

## Out of scope

The following are out of scope for the security policy:

- Vulnerabilities in third-party dependencies that are not specific to the discipline's use of them. Report these to the upstream maintainer.
- Theoretical vulnerabilities without a working proof of concept.
- Social engineering attacks against the discipline's contributors.
- Denial of service attacks against public infrastructure that the discipline does not control.

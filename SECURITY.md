# Security

## Trust model

Community extensions execute code in AiderDesk's Node.js main process without an extension permissions sandbox. They can access files, environment variables, network resources, and subprocesses available to AiderDesk. Folder installation runs `npm install` when `package.json` is present, including applicable lifecycle scripts. Project-only installation is not an operating-system security boundary, and capability labels do not restrict access.

Inspect extensions and their dependencies before installing or updating. Repository inclusion and review do not guarantee safety or constitute a security audit. Use disposable projects and non-sensitive data when evaluating unfamiliar code.

## What to report privately

Examples include credential exposure, unintended disclosure of private project data, injection through untrusted inputs, dangerous dependency installation behavior, and exploitable path handling. Deliberately malicious extension behavior should also be reported privately. The intended ability of trusted extension code to use Node.js is not itself a sandbox escape: there is no extension sandbox to escape.

An ordinary bug (such as incorrect output, a broken command, or a layout problem without security impact) belongs in a public bug report. If unsure whether an issue could expose data or enable abuse, start privately.

## Reporting a vulnerability

Use GitHub's **Report a vulnerability** option in this repository's [Security tab](https://github.com/magiksoft/aiderdesk-community-extensions/security) if private vulnerability reporting is enabled. Otherwise, consult that tab for available private reporting options. If no private route is listed, do not publish exploit details, secrets, or private data in an issue, PR, or discussion; wait for a private reporting route to be provided.

In a private report, include the affected extension and version/commit, AiderDesk version and OS, impact, and minimal reproduction steps using sanitized test data. Do not include live credentials or unrelated private files. Coordinate disclosure privately rather than posting an exploit publicly. If credentials have already leaked, revoke or rotate them promptly.

## Maintainer setup and handling

**Maintainers must enable GitHub private vulnerability reporting** in the repository's **Settings → Security → Advanced Security → Private vulnerability reporting** (GitHub may label the settings page **Code security**). Verify that the Security tab offers **Report a vulnerability** before inviting sensitive reports.

Triage reports privately, assess affected versions and dependencies, and coordinate fixes and disclosure with reporters and relevant upstream maintainers. Document fixes and limitations without exposing sensitive data. This policy does not promise a response deadline or security support for every contributed extension.

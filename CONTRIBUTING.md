# Contributing

Useful, focused extensions and documentation improvements are welcome. For a larger idea, open an extension proposal before investing heavily. Follow the [Code of Conduct](CODE_OF_CONDUCT.md) (currently a draft pending an enforcement contact), and use [private reporting](SECURITY.md) for vulnerabilities.

## Prerequisites and references

- Git and an AiderDesk installation for local testing. Use a disposable project and non-sensitive test data: extension code runs with AiderDesk's privileges.
- Node.js and npm only when needed for dependencies or development tools. The inspected `@aiderdesk/extensions` package requires Node.js 22 or newer; follow the requirements of the package version you use.
- Read the [authoring guide](https://aiderdesk.dev/docs/extensions/creating-extensions) and [API reference](https://aiderdesk.dev/docs/extensions/api-reference). The [application source](https://github.com/magiksoft/aider-desk) is authoritative when documentation differs.
- Real examples include [generate-tests.ts](https://github.com/magiksoft/aider-desk/blob/main/packages/extensions/extensions/generate-tests.ts) and the [folder examples](https://github.com/magiksoft/aider-desk/tree/main/packages/extensions/extensions). Do not copy upstream-only relative imports into a community extension.

There is no root package, shared dependency installation, or repository-wide build/test command. Do not add one just to submit an extension.

## Layout and identifiers

Place extensions directly at the repository root, using either:

- `<extension-id>.ts` or `<extension-id>.js` for a self-contained single-file extension. Put its documentation in `<extension-id>.md` beside it and link it from the README catalog. The UI does not load this sidecar documentation automatically; keep essential usage notes in source comments too.
- `<extension-id>/index.ts` or `index.js` for a folder extension, with `README.md` and any helpers/assets inside that folder. Prefer this form when accompanying files are needed. A `package.json` is **not required** for a folder extension.

These are supported shapes, not sample extensions included in this repository. Discovery checks only root files and immediate child folders; nesting everything inside another `extensions/` directory would require configuring a different source URL.

Export a default class implementing the `Extension` interface (JavaScript should follow the same contract). Import public types from `@aiderdesk/extensions` with `import type`, and runtime exports with ordinary imports when needed. Do not use AiderDesk's internal aliases or private source paths.

Define `static metadata` on the class with at least string `name` and semantic-version `version` fields. Include a useful `description` and `author`; optional supported fields include `capabilities`, `iconUrl`, and `supportedOS`. Keep metadata as a plain object literal with literal values, or reference a same-file variable initialized with such an object. Discovery reads syntax without executing the extension: imported/computed metadata, spreads, and function calls are not a reliable way to supply it. Runtime metadata fallback does **not** make an extension discoverable in the repository UI.

The ID is the filename without `.ts`/`.js`, or the folder name—not the display name or npm package name. Use stable, descriptive lowercase kebab-case IDs and PascalCase class names. Kebab-case is our contribution convention, not a claim that the loader enforces it. Avoid IDs already used here or in upstream sources: installation paths and some UI matching use IDs without repository namespacing. Do not submit both file and folder entries with the same ID. Use a single entry point; `index.ts` takes precedence over `index.js` if both exist.

`capabilities` describes functionality; it grants or restricts no permissions. There is no enforced minimum-AiderDesk-version field in the inspected metadata interface; document tested versions and API requirements instead of inventing metadata keys.

## Dependencies and builds

AiderDesk loads TypeScript/JavaScript through Jiti, compiling TypeScript on the fly. Most extensions need no build step. The loader resolves `@aiderdesk/extensions` and `zod` to AiderDesk's own copies; do not assume an extension-local version overrides those runtime APIs.

For additional runtime npm dependencies, use a folder extension with its own `package.json`. The UI treats any such file as requiring `npm install`, even if it lists no runtime dependencies. Use it only when justified by dependencies or local tooling. Prefer exact direct dependency versions and include `package-lock.json` when using npm; never commit `node_modules/`. Check licensing and installation scripts of transitive dependencies too.

The installer copies the folder and runs **`npm install`**, not `npm ci` and not a general build command. Lifecycle scripts may execute. Avoid install-time downloads, native builds, or scripts unless necessary, reviewed, and clearly documented. Do not assume a `main` field pointing to `dist/` changes the required root `index.ts`/`index.js` entry point.

If generated files are essential at runtime, include the necessary reviewed artifacts or document a deliberately reviewed installation mechanism. Keep source and reproduction instructions, and avoid unrelated build outputs. Do not add a build system for ordinary TypeScript loading. External UI libraries loaded through AiderDesk's library mechanism are dependencies too: disclose their packages, remote delivery (such as esm.sh), and first-load network needs.

## Local development and testing

1. Clone your fork and create a branch. Add the extension in one of the supported shapes above; do not install the repository root as an extension.
2. Copy the single file or complete extension folder into a disposable project's `.aider-desk/extensions/`. Global testing in `~/.aider-desk/extensions/` is also supported, but affects all projects. If the folder has `package.json`, inspect it and then run `npm install` in the copied folder before loading it.
3. For editor/type support, use `@aiderdesk/extensions` in an extension-local or separate development workspace as needed. Do not create a root package solely for types. Run type checks against the SDK version matching the AiderDesk version under test; type imports alone do not require a runtime dependency installation.
4. Edit the installed copy, or recopy changes there. AiderDesk watches extension directories and hot reloads changed code; check its logs and **Settings → Extensions → Installed** for load errors. Source-only changes in this checkout are not watched unless you also install/copy them.
5. Exercise actual commands, tools, UI, and event handlers, including invalid input, missing credentials, failed external services, and any supported platform-specific behavior. Check reload/unload cleanup for timers, watchers, and child processes; use `context.addDisposable()` or `onUnload()` as appropriate.
6. Copy tested source changes back into your branch. Remove the disposable installation when finished. Do not test untrusted extension code against real credentials or valuable data.

For UI discovery/installation testing of a contribution, the application needs a published HTTPS GitHub repository URL; a local filesystem path is not a repository source. A root URL's single-file downloads use `main`, while discovery clones the repository's default branch. Keep these aligned when testing a fork. Use [the documented UI flow](README.md#browse-and-install) and record what was tested; do not push or publish automatically just to validate a change.

There is no required `extensions.json`: the UI scanner obtains entries from source metadata. In the application, that file only supplements install counts; the upstream package CLI uses its own catalog. Do not add a generated catalog or fabricated counts here.

## Extension documentation

For every extension, document:

- Purpose, maintainer/author, and a short example of use.
- Installation location, configuration, commands/tools/UI, and how to disable or remove it.
- Runtime dependencies, optional development tools, build steps/artifacts, external executables, and supported operating systems.
- External services, accounts, network endpoints, credentials required, and any costs.
- Credential storage/access, what data leaves the machine, logging/retention, and other privacy implications. State explicitly when no external services or credentials are needed.
- Known limitations, failure modes, tested AiderDesk version(s), and compatibility requirements.
- License, third-party assets/code, attribution, and any approved licensing exception.

Update the root README catalog with a concise description and a relative link to the documentation. Never claim universal compatibility or a security audit without evidence.

## Security expectations

Extensions are trusted code, not sandboxed plugins. Use the least access needed even where the host does not enforce it. Validate inputs and paths, avoid shell interpolation of untrusted input, and make destructive actions explicit. Do not bypass host tool approvals; follow the documented approval APIs where applicable. Hooks and direct Node.js operations can run outside agent tool approval, so do not rely on that approval as an extension sandbox.

Never embed credentials, tokens, personal data, or real `.env` files in source, fixtures, screenshots, or logs. Document a suitable credential mechanism for the extension (for example, environment variables or an existing host API) without inventing a secure store API. Do not assume ordinary configuration files or environment variables are encrypted secret storage. Redact secrets before logging; do not send project content to services without clear disclosure. Report vulnerabilities through [SECURITY.md](SECURITY.md).

## Before opening a pull request

- Confirm the entry point and literal metadata are discoverable; compare IDs with existing and upstream entries.
- Test loading and relevant behavior in AiderDesk, including reload/unload and error paths. State the tested AiderDesk version and OS, and explain any untested behavior.
- Run the extension's documented type checks, tests, and build when present (for example, `npm run typecheck` only if that script exists). Add focused tests for non-trivial logic where practical; never invent a passing repository-wide command.
- Check dependencies, lifecycle scripts, licensing, credentials, privacy disclosures, and required runtime files.
- Check relative documentation links, examples, and catalog updates. Run `git diff --check` for tracked changes and inspect `git status --short`, including new files, for secrets and generated clutter.

Keep PRs focused. The [PR template](.github/pull_request_template.md) provides a short checklist for code and documentation changes. Review checks behavior, maintainability, documentation, compatibility, and trust risks; acceptance is not a guarantee of safety.

## Maintenance and licensing

Authors should respond to actionable reports and document maintenance limitations. Bump metadata versions when behavior changes; keep any package version consistent where applicable. Explain breaking changes, migration steps, and changed dependencies or data access. Retain stable IDs unless a deliberate migration is documented. Maintainers may mark an extension unmaintained or remove it when it no longer works or poses unacceptable risks.

Contributions are submitted under the repository's [MIT license](LICENSE), unless an explicitly documented and approved exception applies. Only contribute material you have the right to license. Preserve existing copyright notices, third-party licenses, and required attribution, including for images and other assets. Identify any incompatibility or proposed exception in the PR before inclusion; do not silently relicense third-party work. No CLA is required.

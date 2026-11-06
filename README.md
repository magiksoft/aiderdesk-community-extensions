# AiderDesk Community Extensions

A welcoming home for community-contributed extensions for [AiderDesk](https://aiderdesk.dev), maintained separately from the [AiderDesk application](https://github.com/magiksoft/aider-desk). Contributions that make everyday workflows more useful are welcome.

> **Trust before installing.** Extensions execute code in AiderDesk's Node.js main process, without an extension permissions sandbox. They can read and write files, access environment variables and credentials available to the process, make network requests, and launch subprocesses with AiderDesk's privileges. Installing a folder with `package.json` runs `npm install`, which can also execute dependency lifecycle scripts. Inspect and trust the extension and its dependencies before installing or updating. Community contributions are not necessarily official or security-audited; review does not guarantee safety. Capability labels are descriptive, not access controls.

## Browse and install

1. Open AiderDesk **Settings → Extensions → Available**.
2. If needed, enter this exact repository URL in the repository field and click **Add**:

   ```text
   https://github.com/magiksoft/aiderdesk-community-extensions
   ```

   Use the full HTTPS GitHub URL, not the shorthand `magiksoft/aiderdesk-community-extensions` or a raw file URL.
3. Use the context arrows to select **Global** (all projects) or an open project. Browse/search available extensions, inspect their source and documentation, and click **Install** for an extension you trust.
4. Manage installed extensions in **Installed**. Use **Refresh** to refresh the available list.

Git must be available to AiderDesk for repository discovery; npm is also needed when an extension includes `package.json`. Global installations live in `~/.aider-desk/extensions/`; project installations live in `<project>/.aider-desk/extensions/`. Project scope controls where an extension is loaded, not its operating-system privileges.

**Inclusion as a default source is planned, not implemented.** Add this repository manually for now. The package CLI's built-in catalog is separate; it does not automatically browse this repository.

## Extension catalog

No extensions have been contributed yet. An empty catalog is valid; until entries exist, AiderDesk may not display a repository group even after the source is added.

Want to contribute? Start with [CONTRIBUTING.md](CONTRIBUTING.md), the [authoring guide](https://aiderdesk.dev/docs/extensions/creating-extensions), or the [upstream examples](https://github.com/magiksoft/aider-desk/tree/main/packages/extensions/extensions).

## Repository structure

- Root `*.ts` / `*.js`: single-file extensions, once contributed.
- Root `<extension-id>/index.ts` or `index.js`: folder extensions, with their own README and any assets/helpers. `package.json` is optional.
- [CONTRIBUTING.md](CONTRIBUTING.md): authoring, testing, and review expectations.
- [SECURITY.md](SECURITY.md): private vulnerability reporting and trust boundaries.
- [LICENSE](LICENSE): MIT license; preserve any documented third-party licenses and attribution.
- `.github/`: lightweight issue and pull request templates.

AiderDesk scans root entries and immediate child extension folders; there is no required manifest, generated index, root package, or repository-wide build. Keep the published default branch named `main`: root-URL single-file installation downloads from that branch.

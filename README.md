# gh-utils-lenik

GitHub CLI helper tools: refresh repository contributors list and create releases from `debian/changelog`.

**Author:** Lenik <gh-utils-lenik@bodz.net>  
**Copyright:** 2026  
**License:** GPL-2.0-or-later

## Requirements

- Git
- [GitHub CLI](https://cli.github.com/) (`gh`), authenticated (`gh auth login`)
- Repository with remote `origin` pointing to the GitHub repo

## Tools

### gh-refresh-contributors

Force GitHub to refresh the repository contributors list by temporarily renaming the default branch and renaming it back.

```bash
gh-refresh-contributors [OPTIONS]
```

| Option | Description |
|--------|-------------|
| `-b`, `--branch NAME` | Branch to use (default: auto-detect main or master) |
| `-t`, `--tmp-branch NAME` | Temporary branch name (default: temp-refresh-branch) |
| `-v`, `--verbose` | Verbose logging |
| `-q`, `--quiet` | Less output |
| `-h`, `--help` | Show help |

### gh-makerelease

Create a GitHub release with auto-detected project type. Detects debian (if `debian/control` exists), vsix (if `package.json` contains `vsce package` script), or nodejs (if `package.json` exists) projects.

```bash
gh-makerelease [OPTIONS]
```

| Option | Description |
|--------|-------------|
| `-f`, `--force` | Replace existing release and tag if present |
| `-v`, `--verbose` | Verbose logging |
| `-q`, `--quiet` | Less output |
| `-h`, `--help` | Show help |

**Project type detection:**

| Type | Version Source | Build Command | Release Attachments |
|------|---------------|---------------|---------------------|
| debian | `debian/changelog` | `debuild -b -us -uc` | `{pkg}_{version}_*.deb` + source tarball |
| vsix | `package.json` | `pnpm package` | `{pkg}-{version}.vsix` |
| nodejs | `package.json` | `pnpm package` | `{pkg}-{version}-dist.zip` |

**Release notes:**

- **debian**: Extracted from `debian/changelog` header
- **vsix/nodejs**: Git log message from most recent commit

Run from the repo root.

## Install

### From source

Copy the scripts into your `PATH`:

```bash
cp gh-refresh-contributors gh-makerelease /usr/local/bin/
chmod +x /usr/local/bin/gh-refresh-contributors /usr/local/bin/gh-makerelease
```

Optional: install man pages and bash completions from `debian/*.1` and `bash-completion/`.

### Debian package

```bash
dpkg-buildpackage -us -uc -b
sudo dpkg -i ../gh-utils-lenik_*.deb
```

```

## How gh-refresh-contributors works

1. Rename the local branch to the temporary name and push (so the temp branch exists on the remote).
2. Set GitHub default branch to the temporary name.
3. Delete the old branch on the remote.
4. Rename the local branch back and push.
5. Set GitHub default branch back to the original name.
6. Delete the remote temporary branch.

Each step checks the current state so the script can resume if interrupted.

## License

GPL-2.0-or-later. See [debian/copyright](debian/copyright) for the full license text.

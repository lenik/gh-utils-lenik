# gh-refresh-contributors

Force GitHub to refresh the repository contributors list by temporarily renaming the default branch and renaming it back.

**Author:** Lenik <gh-refresh-contributors@bodz.net>  
**Copyright:** 2026  
**License:** GPL-2.0-or-later

## Requirements

- Git
- [GitHub CLI](https://cli.github.com/) (`gh`), authenticated (`gh auth login`)
- Repository with remote `origin` pointing to the GitHub repo

## Usage

Run from the root of your git repository:

```bash
gh-refresh-contributors [OPTIONS]
```

### Options

| Option | Description |
|--------|-------------|
| `-b`, `--branch NAME` | Branch to use (default: auto-detect main or master) |
| `-t`, `--tmp-branch NAME` | Temporary branch name (default: temp-refresh-branch) |
| `-v`, `--verbose` | Verbose logging |
| `-q`, `--quiet` | Less output |
| `-h`, `--help` | Show help |

## Install

### From source

Copy the script into your `PATH`:

```bash
cp gh-refresh-contributors /usr/local/bin/
chmod +x /usr/local/bin/gh-refresh-contributors
```

Optional: install man page and bash completion with `make install` (or copy from `debian/gh-refresh-contributors.1` and `bash-completion/gh-refresh-contributors`).

### Debian package

```bash
dpkg-buildpackage -us -uc -b
sudo dpkg -i ../gh-refresh-contributors_1.0-1_all.deb
```

## Create the GitHub repository

To create the GitHub repo for this project (one-time):

```bash
make create-gh-repo
```

Or manually:

```bash
gh repo create gh-refresh-contributors --public --source=. --remote=origin --push
```

If the repo already exists elsewhere, clone and add the GitHub remote:

```bash
gh repo create gh-refresh-contributors --public --clone
cd gh-refresh-contributors
git remote add upstream <your-existing-repo-url>
git push -u origin main
```

## How it works

1. Rename the local branch to the temporary name and push (so the temp branch exists on the remote).
2. Set GitHub default branch to the temporary name.
3. Delete the old branch on the remote.
4. Rename the local branch back and push.
5. Set GitHub default branch back to the original name.
6. Delete the remote temporary branch.

Each step checks the current state so the script can resume if interrupted.

## License

GPL-2.0-or-later. See [debian/copyright](debian/copyright) for the full license text.

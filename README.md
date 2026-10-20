# Bytekodex workspace

This repository is two things at once.

For GitHub it is the organization's `.github` repository: the profile shown on
[github.com/bytekodex](https://github.com/bytekodex) lives in [`profile/README.md`](profile/README.md), and
reusable workflows live in [`.github/workflows`](.github/workflows).

For us it is the **superproject**: every Bytekodex repository is wired in here as a submodule, so one clone
gives a single workspace while each repository keeps its own history, releases and CI. Nothing is vendored —
a submodule is only a pinned commit, and the projects talk to each other through versioned artifacts
(container images for the platform library, a Go module for the binding), never through relative paths.

## Layout

| Path | Repository | Language | Role |
| --- | --- | --- | --- |
| `platform/` | [bytekodex-platform](https://github.com/bytekodex/bytekodex-platform) | Rust | Bytecode readers, token IR, image rendering, C ABI |
| `telegram/` | [bytekodex-telegram](https://github.com/bytekodex/bytekodex-telegram) | Go | Telegram bot, compilation orchestration |
| `reference/painter/` | [bytekodex-painter](https://github.com/bytekodex/bytekodex-painter) | Rust | Archived. The original ANTLR + Skia painter, kept for its palette and FFI |
| `reference/antlr/` | [bytekodex-antlr](https://github.com/bytekodex/bytekodex-antlr) | ANTLR | Archived. The original grammars |

## Clone

```shell
git clone --recurse-submodules https://github.com/bytekodex/.github.git bytekodex
```

The two `reference/*` submodules are registered as shallow and are not needed for day-to-day work, so a
lighter clone is usually what you want:

```shell
git clone https://github.com/bytekodex/.github.git bytekodex
cd bytekodex
git submodule update --init platform telegram
```

## Editor setup

Open [`bytekodex.code-workspace`](bytekodex.code-workspace) in VS Code rather than the plain
folder to see every submodule as its own root with one shared settings and extension-recommendation
set. Each submodule also carries its own `.vscode/settings.json` for whoever opens just that one
repository on its own.

## Working in the workspace

Submodules stay independent, which means the usual rule applies: commit and push inside the submodule first,
then record the new pointer in the superproject.

```shell
# inside platform/, telegram/, ... — a normal repository, nothing special
git switch -c my-change && git commit -am '...' && git push

# back at the root, record where the submodules now point
cd .. && git add platform && git commit -m 'platform: bump to <sha>'
```

To pull everything to the latest upstream state:

```shell
git pull && git submodule update --init --remote --recursive
```

Set this once so `git status` at the root summarizes dirty submodules instead of staying silent:

```shell
git config --global status.submoduleSummary true
git config --global diff.submodule log
git config --global submodule.recurse true
```

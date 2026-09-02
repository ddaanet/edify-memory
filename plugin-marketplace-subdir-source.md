---
name: plugin-marketplace-subdir-source
description: "a plugin's skills never appear in any consuming repo, install and `/plugin marketplace update` both reporting success: `marketplace.json` used `\"source\": \"github\"`, which resolves the repo root only and takes no `path`, so a plugin under a subdirectory needs `git-subdir` + `url` + `path`; tell is a cache dir holding the repo root with no `.claude-plugin/plugin.json`"
metadata: 
  node_type: memory
  type: reference
  originSessionId: bc3d74bc-b5a8-435b-8d74-3cfa28bc27a6
  modified: 2026-09-02T15:06:18.065Z
---

A marketplace entry's `github` source type resolves the **repo root** and
accepts no `path` field. A plugin whose `.claude-plugin/plugin.json` sits in a
subdirectory therefore installs to nothing: the client clones the repo, finds no
manifest at the top level, and reports success. Only `git-subdir` takes a path.

```json
"source": { "source": "git-subdir", "url": "owner/repo", "path": "plugin" }
```

`url` accepts the `owner/repo` shorthand as well as a full git or SSH URL, and
the checkout is sparse — it fetches only `path`. Any repo publishing more than
one plugin is in this shape by construction: the `ddaa*` entries all point into
subdirectories of `ddaanet/skills`, and are the working example to copy.

**The symptom is silence.** No error at install, no error at session start; the
plugin's skills simply never appear in the consuming repo. It is invisible from
the publishing repo, where the plugin is loaded from a local path and works.

**How to apply:**

- Confirm by listing the extraction, not the manifest:
  `ls ~/.claude/plugins/cache/<owner>/<plugin>/<version>/`. If that directory
  is the *repo root* rather than the plugin — no `.claude-plugin/plugin.json`
  in it — the source type is wrong.
- **A source fix alone does not reach anyone.** The install cache is keyed by
  version, so correcting the source under an unchanged version re-fetches
  nothing. `/plugin uninstall` then `/plugin install`, or bump the version.
  See [[stale-plugin-code]] for the rest of that keying.
- Sparse checkout also sidesteps [[plugin-recurse-clone]]: `github` clones the
  whole repo `--recurse-submodules`, so a private or SSH-url submodule breaks
  install for everyone; `git-subdir` never fetches it.
- **A release script that writes the entry must know the subdirectory.** Derive
  it from the manifest path (`dirname $(dirname "$manifest")`, `.` at the root)
  and emit `github` or `git-subdir` accordingly. Guard the *existing* entry too
  — the creation branch fires once and never again, so it is not where the
  regression gets caught.

Verified against the plugin-marketplaces docs and a live edify install, 2026-09-02.

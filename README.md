# GoML package registry

This is the default central package index for GoML. It maps canonical module names to public repositories in the [gomlang organization](https://github.com/gomlang).

Packages currently follow their repository's default branch. There are no package versions, version constraints, release tags, or minimum-version selection in this index. Canonical names come from each repository's root `goml.toml`; a name such as `ecosystem::ansi` is independent of its GitHub organization.

Use a source-built GoML toolchain at commit
[`051c79b74497dc1f3095210b0e358990118fccfa`](https://github.com/gomlang/goml/commit/051c79b74497dc1f3095210b0e358990118fccfa),
the current ecosystem verification baseline. The published GoML 0.1.59 release
does **not** support this unversioned index; it is only the bootstrap compiler
for that source build. See the
[shared toolchain installation instructions](https://github.com/gomlang/workflows/blob/main/ci/README.md)
for the pinned bootstrap and source-build procedure.

With that toolchain:

```sh
goml add ecosystem::ansi
goml update
goml test
```

The dependency declaration is:

```toml
[dependencies]
"ecosystem::ansi" = true
```

`goml add` fetches the index and the required dependency sources. `goml update` refreshes the index and the current module's normal and development dependency closure. Outside a module it updates only the index. Builds and editor queries use the cached sources without accessing the network.

Each index entry has one Git source:

```toml
[modules."ecosystem::ansi"]
git = "https://github.com/gomlang/ansi.git"
```

Add a module by adding an entry to `index.toml`. Its repository must contain a root `goml.toml` with a matching `[module].path`. All normal and development dependencies must also be indexed. The driver validates module identities and rejects production dependency cycles.

The cache defaults to `~/.goml/cache/registry`, or `$GOML_HOME/cache/registry`. The index checkout lives there, with package checkouts under `sources/<owner>/<module>/`. Local source changes in those checkouts are not discarded by updates.

Older GoML toolchains expect versioned registry entries and cannot consume this index. Existing package manifests with legacy version strings remain readable by the new toolchain during migration; those strings no longer select a version.

Shared test helpers are published as `ecosystem::testkit`. The legacy
`ecosystem::verification` entry remains available for historical consumers;
new tests should depend on testkit. Workflow infrastructure is not a registry package.

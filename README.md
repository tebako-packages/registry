# tebako-packages/registry — the official tebako registry

The machine-consumable **official registry** for the tebako ecosystem
(spec 37 §6): one flat `tpkg-registry.yaml` aggregating every
`tebako-packages` feedstock's payload rows.

```console
$ tebako add-registry tfs:github:tebako-packages/registry
$ tebako install metanorma
```

`tebako setup` (the installer flow) seeds exactly this registry as the
`official` book entry.

## Never hand-edit `tpkg-registry.yaml`

The authored authority (SSOT) for every row is the **feedstock's own**
`tpkg-registry.yaml` — this file is a generated aggregate, rebuilt by
`tools/aggregate.rb`:

```console
$ ruby tools/aggregate.rb           # regenerate in place
$ ruby tools/aggregate.rb --check   # parity assertion (CI)
```

- Rows flow **verbatim**, including `status: withdrawn`.
- A payload name carried by several feedstocks aggregates **once** when
  every carrier's row is identical (DEPENDS-closure mirroring —
  e.g. metanorma mirroring the inkscape toolkit it requires); divergent
  rows for one name abort the run, naming the carriers. The
  aggregate never applies a priority ordering, because it is
  internally unambiguous by construction.
- `# source:` comments record each row's feedstock (provenance only;
  resolvers ignore comments).
- The workflow regenerates on a schedule and on demand, commits any
  drift, and runs `--check` on every push and pull request, so a
  hand-edit goes red.

## Adding a feedstock to the catalog

List it in `feedstocks.txt` (one repo name per line). Its rows appear
in the aggregate on the next run. Removal works the same way in
reverse.

For the org's human-facing catalog, conventions, and the feedstock
template, see [tebako-packages/index](https://github.com/tebako-packages/index).

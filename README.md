# Bram hardware recipes

Measured hardware configurations for [Bram](https://github.com/KuRue/bram), the local-first Android
LLM client: the JSON Schemas a recipe is written against, a fixture corpus that pins what is accepted
and refused, the checker that keeps both honest, and the recipes themselves.

A recipe names one measured configuration for one device, engine build, and model — not a guess
about a phone family. Bram matches, previews, and revalidates it on the phone before any setting is
applied. A signature checks who published a recipe, never whether its numbers are good.

## Contributing

[`CONTRIBUTING.md`](CONTRIBUTING.md) covers the measurement, the evidence a recipe has to carry, and
what maintainers check. In short:

```bash
python3 validate.py path/to/your-recipe.json   # one document
python3 validate.py                            # the corpus, and the index here
```

It needs `jsonschema` (`pip install jsonschema`). CI runs the second form on every pull request.

## What lives where

- `schema/` — one recipe document and one source index, as JSON Schema.
- `fixtures/` — documents that must be accepted and refused, each invalid case naming the field it
  must be refused at. Bram's Kotlin parser is checked against the same corpus, so a rule that changes
  on one side fails there instead of surprising a contributor.
- `validate.py` — the checker. The schema, fixtures, and validator are copied byte for byte from the
  app repository, where format changes land first; only the READMEs and `CONTRIBUTING.md` examples
  name their own paths.
- `index.json` — the recipes published here, with each file's SHA-256.
- `samsung-sm-s938-sm8750-lfm2.5-2.6b-q4_0-cpu.json` — the first measured recipe: the owner's
  Galaxy S25 Ultra running LFM2.5-2.6B Q4_0 on the CPU.

## Signing

Every index here is published with a detached `index.signature.json`, signed with the repository
publisher's P-256 key over the exact index bytes. The app shows the key's SHA-256 fingerprint and
accepts nothing until the person approves that key; there is no unsigned fallback. The protocol and
the helper are in
[`tools/recipe-signing`](https://github.com/KuRue/bram/tree/main/tools/recipe-signing).

Add this repository in **Settings → Hardware → Recipe sources** as
`KuRue/bram-hardware-recipes@<full commit>`, which reads the files at that immutable commit. The
current publisher key fingerprint is
`ab1343f3b582daf585095d58b2f24a537eec6c430c46f5b67192a3f563645b08`; compare it with the app's
prompt, and with the owner through a separate channel. The private key is held outside this
repository.

CI verifies `index.signature.json` against the exact index bytes. A pull request that changes
`index.json` must therefore include a fresh signature, made after the last edit; otherwise the check
fails, and an update signed before the edit would be refused by the app.

The app's bundled snapshot remains the reviewed default; this repository is an external source a
person chooses to add.

## License

Apache-2.0; see [`LICENSE`](LICENSE).

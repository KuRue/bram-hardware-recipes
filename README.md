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

Bram downloads a network source only with a detached `index.signature.json` beside the index, signed
with the publisher's P-256 key. The app shows the key's SHA-256 fingerprint and accepts nothing until
the person approves that key; there is no unsigned fallback. The protocol, the helper, and how a
reader checks a fingerprint are in
[`tools/recipe-signing`](https://github.com/KuRue/bram/tree/main/tools/recipe-signing).

The app's bundled snapshot remains the reviewed default. This repository's `index.json` is the
contribution target and becomes a source a person can add once its publisher signs a release with a
key held outside this repository.

## License

Apache-2.0; see [`LICENSE`](LICENSE).

# pubsub-node

Rust implementation of the Cardano PubSub node — the reference prototype behind the experiments in [CIP-0201](../docs/cip/README.md). It runs the protocol-core state machine deterministically (prototype node and simulator share the same pure transitions), configures the five evaluated dissemination models, and drives the experiment sweeps. The protocol design itself lives in the parent repo under `docs/` and `formal_spec/`.

## Status

Reference implementation of the protocol core and the experiment driver. Signing is a mock scheme and both registries are held in memory (rule-by-rule correspondence in [cip-correspondence](docs/cip-correspondence.md)); the on-chain components remain to be implemented. The spec-driven feature list lives in [`specs/`](specs/).

## For contributors

1. Read the [project constitution](.specify/memory/constitution.md) — five principles governing implementation work in this directory. Read this before authoring code or specs.
2. Read [`CLAUDE.md`](CLAUDE.md) for the Spec Kit feature-development workflow and the manual branch step required in this layout.
3. Background on the protocol: [`docs/`](../docs/) holds the design synthesis, gap analysis, and extension proposals; [`formal_spec/`](../formal_spec/) holds the Quint and PRISM models.

## Spec Kit workflow

Features flow through:

    /speckit-specify  →  /speckit-plan  →  /speckit-tasks  →  /speckit-implement

Before each `/speckit-specify`, cut the feature branch manually (Spec Kit can't auto-create it in this nested layout — see [`CLAUDE.md`](CLAUDE.md) for the rationale):

    ls specs/                              # peek at the next available number
    git checkout -b NNN-<short-name>       # e.g. 001-minimal-node-scaffold

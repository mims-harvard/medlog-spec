# MedLog specifications

This repository contains the language-neutral specifications used by MedLog:

- [openapi.yaml](openapi.yaml): event API and payload schemas.
- [semconv/medlog.yaml](semconv/medlog.yaml) and [manifest](semconv/manifest.yaml):
  the Weaver registry and its pinned OpenTelemetry dependency.
- [semantic_schemas/](semantic_schemas/README.md): four unmodified OpenTelemetry
  v1.40.0 GenAI JSON schemas, with the upstream Apache-2.0 [license](semantic_schemas/LICENSE).

Request validation loads local files and needs no network access. From this
repository root, validate the registry with Weaver 0.26.1:

```sh
weaver registry check -r semconv
```

Weaver resolves the pinned upstream registry over the network. From the consuming
medlog-system repository, run `weaver registry check -r spec/semconv` instead.

## Receiver integration

Keep this directory at `spec/`, alongside `event-receiver/`. The receiver's path
module resolves contracts relative to its own location, not the working directory.
The Docker image and offline bundle preserve this layout. These files are not a
Python package and are not included in the receiver's wheel.

Specification changes must be coordinated with receiver mappings, regression tests and
interoperability documentation in the consuming repository. Relocation alone must
preserve schema contents, versions and identifiers.

## Working with the repository

Clone this repository to edit the specifications directly:

```sh
git clone --branch main git@github.com:mims-harvard/medlog-spec.git
cd medlog-spec
```

The medlog-system repository consumes this repository as its `spec/` submodule,
configured to follow `main`. From the medlog-system repository root:

```sh
# Fetch the recorded specification revision after cloning.
git submodule update --init --recursive spec

# Fetch the latest specification revision on main.
git submodule update --init --remote spec
```

Git records a specific submodule commit even when a tracking branch is configured.
Review specification updates with the receiver's regression tests, then commit the updated
`spec` pointer in medlog-system. Publish specification commits before committing a
consumer update so other checkouts can fetch them.

Offline bundles must include the specification files and license so deployment needs
neither Git nor network access to load the contracts.

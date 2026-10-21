# MedLog schemas

This repository contains the language-neutral contracts used by MedLog:

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
medlog-system repository, run `weaver registry check -r schemas/semconv` instead.

## Receiver integration

Keep this directory at `schemas/`, alongside `event-receiver/`. The receiver's path
module resolves contracts relative to its own location, not the working directory.
The Docker image and offline bundle preserve this layout. These files are not a
Python package and are not included in the receiver's wheel.

Schema changes must be coordinated with receiver mappings, regression tests and
interoperability documentation in the consuming repository. Relocation alone must
preserve schema contents, versions and identifiers.

## Working with the repository

Clone this repository to edit the contracts directly:

```sh
git clone --branch main git@github.com:mims-harvard/medlog-spec.git
cd medlog-spec
```

The medlog-system repository consumes this repository as its `schemas/` submodule,
configured to follow `main`. From the medlog-system repository root:

```sh
# Fetch the recorded schema revision after cloning.
git submodule update --init --recursive schemas

# Fetch the latest schema revision on main.
git submodule update --init --remote schemas
```

Git records a specific submodule commit even when a tracking branch is configured.
Review schema updates with the receiver's regression tests, then commit the updated
`schemas` pointer in medlog-system. Publish schema commits before committing a
consumer update so other checkouts can fetch them.

Offline bundles must include the schema files and license so deployment needs
neither Git nor network access to load the contracts.

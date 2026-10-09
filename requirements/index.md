# README

This repository is a compiler tool that takes a GovStack repository as input and checks and compiles it.

# First milestone: Publication rendering

- index#req-1: Given a repository for a GovStack Publication ensure the following structure is present:
```sh
.
├── ADR/
├── assets/
├── introduction/
├── chapters/       or requirements/ for a specification
├── resources/
├── metadata.yml
├── authors.yml
└── toc.yml
```

- index#req-2: read the metadata.yml
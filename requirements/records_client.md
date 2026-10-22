# Records client

Reads GovStack Records. It is set in the GovStack Process, chapter *GovStack
Records*. It is kept as YAML files in the `GovStackWorkingGroup/govstack-records` repository. A
GovStack Records server is planned but not hosted yet, so the client reads the files.

- records_client#req-1: read GovStack Records from the default branch of
  `GovStackWorkingGroup/govstack-records`. Accept a local path instead with `--records <path>`,
  for local runs and tests.
- records_client#req-2: read GovStack Records once per run and keep it in memory.
- records_client#req-3: fail the stage, not the run, if GovStack Records cannot be read. The checks
  that need it are reported as skipped (`index#req-6`).
- records_client#req-4: hide the source of GovStack Records behind one interface, so that the
  GovStack Records server can replace the files when it is hosted (`GET /<urn>`,
  `GET /<urn>/versions/<version>`).

## Lookups

- records_client#req-5: find Working Groups in `working_groups.yml` under `govstack: wg:` and
  teams in `teams.yml` under `govstack: team:`, by `id`. Return the record with its `status`.
- records_client#req-6: find the proposal records of a `urn` in `records/proposals/`, by the `urn`
  field of each file.
- records_client#req-7: find the release records of a `urn` in `records/releases/`, by the `urn`
  field of each file. Return each with its `version`, `url` and `date`.
- records_client#req-8: find the obsoletion record of a `urn` in `records/obsoletions/`.
- records_client#req-9: find a person in `persons.yml` under `govstack: person:`, by `id` and by
  `name`.
- records_client#req-10: read records by their `urn` field, not by their file name. Warn if a file
  name does not follow `<YYYY-MM-DD>-<name>.yml` (*GovStack Records*, *Records*).

## Open

- [ ] `publications.yml` does not exist yet. The client reads the records directly, which gives the
  same answer. Switch to `publications.yml` when it exists?
- [ ] A private clone of `govstack-records` needs a token. Is the repository public?

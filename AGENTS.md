# AGENTS.md

## Project overview

Metadata for the LEGEND-1000 experiment, as YAML. This is data, not code.

Unlike [legend-metadata](https://github.com/legend-exp/legend-metadata), which
is a superrepo of submodules, this repository holds everything inline. The
metadata format is specified at
[legend-data-format-specs](https://legend-exp.github.io/legend-data-format-specs/dev/metadata).
It is read through [pylegendmeta](https://github.com/legend-exp/pylegendmeta),
which clones and sets up the repository on the caller's behalf.

## Repository structure

- `datasets/` → high-level dataset metadata
  - `runinfo.yaml` → every run, with its start time and live time
  - `runlists.yaml` → named datasets (lists of runs)
  - `statuses/` → per-run detector analysis statuses
- `hardware/`
  - `configuration/channelmaps/` → the channel map: one entry per readout
    channel, keyed by detector name, holding `system`, `location` and `daq`
  - `detectors/germanium/diodes/` → one YAML per HPGe detector (`production`,
    `geometry`, `characterization`)
  - `detectors/germanium/crystals/` → one YAML per crystal (impurity profile,
    slices)
- `simprod/config/` → the configuration driving the
  [legend-simflow](https://github.com/legend-exp/legend-simflow) simulations
  production, organized by "experiment" (`l1000dsg01`)
  - `geom/` → the simulated geometry, built by
    [legend-pygeom-l1000](https://github.com/legend-exp/legend-pygeom-l1000)
  - `pars/<exp>/geds/` → simulation parameters for the HPGe detectors
    (`aoeresmod`, `currmod`, `elecmod`, `eresmod`, `opv`, `psdcuts`, `ssd`)
  - `tier/<exp>/` → settings for each tier of the Simflow (`stp`, `opt`, `hit`,
    `cvt`, `evt`, `pdf`)

## Dummy records

The experiment does not exist yet, so the repository holds one dummy record per
kind of channel instead of the real inventory:

- `V99999Z` → the germanium diode, with `V99999` its crystal
- `S9999Z` → the SiPM reading out fiber module `S9999`
- `PMT9999` → the muon-veto PMT

`Legend1000Metadata` in pylegendmeta returns an adjusted copy of a dummy record
for any name matching its pattern, so one record stands in for a whole array:
asking for `V12345A` gives the `V99999Z` record with `name`, `location`,
`production` and `daq` rewritten to match. The SiPM and PMT channels exist in
the channel map and the statuses but have no record under `hardware/detectors/`.

Keep these records generic. Do not add real detectors next to them, and do not
give a dummy a value that only one real device would have.

## Validity

Every directory holding time-dependent files carries a `validity.yaml` saying
which file applies from which timestamp. The `%` in a filename is a wildcard for
the period, run or type. It is intentional — do not escape or rename it.

## Checks

pre-commit runs on pull requests through pre-commit.ci. Run it locally before
committing, let it auto-fix what it can, and fix what it reports:

```console
$ pre-commit install
$ pre-commit run --all-files
```

The hooks that matter here (`.pre-commit-config.yaml`):

- `validate-validity` (pylegendmeta) checks every `validity.yaml`.
- `forbid-new-submodules` — this repository is a monorepo by design.
- prettier (`--prose-wrap=always`) formats YAML and Markdown. Let it own
  wrapping and indentation.
- codespell checks prose spelling.

## Conventions

- One detector or component per file. The filename is `<name>.yaml` and matches
  the `name` field inside.
- Units are encoded in field names (`_in_mm`, `_in_V`, `_in_g`, `_in_ns`, ...).
- Quote values that would otherwise read as numbers or booleans: the `crystal`
  value (`"999"`), dates (`"YYYY-MM-DD"`), `usability: "on"`.
- Keep keys in the order used by existing files. Do not reorder them.
- Keep the relevant `README.md` in sync when adding or changing a field.

## Boundaries

- Make sure pre-commit passes before committing.
- Only change files within the scope of the task.
- Do not commit `.DS_Store` or `.claude/`.

# OneSettings Flight Monitor

OneSettings Flight Monitor tracks changes in Microsoft OneSettings payloads that are used by Windows to control feature rollout, service behavior, and configuration changes.

The goal of this repository is simple: make hidden OneSettings changes easier to spot, compare, and investigate over time.

Windows can already have the code for a new feature or fix installed locally, while the feature itself remains inactive until Microsoft sends the right configuration signal. OneSettings is one of the places where those signals can show up. This monitor watches those payloads and records what changed.

## What this repository contains

This repository contains generated change reports for OneSettings payloads.

The reports are stored in the `changes` folder and are named with the UTC timestamp of the detected change.

Example:

```text
changes/2026-07-03T04-00-04Z.md
```

Each report shows what changed during that run, such as:

```text
FeatureConfig added
FeatureConfig removed
FeatureTuning removed
SCC InstallService settings changed
```

## What it monitors

The monitor looks for changes in OneSettings related payloads, including but not limited to:

```text
FeatureConfig.json
FeatureTuning.json
SCCInstallService.json
```

These files can contain feature identifiers, payload keys, rollout variants, tuning values, service settings, and other configuration data that Windows components may use.

## Why this matters

A Windows update can install new code without immediately enabling the feature.

That means:

```text
Installed does not always mean active.
```

Feature rollout can depend on configuration data that is refreshed later. When that configuration changes, Windows may suddenly start behaving differently, even though no new cumulative update was installed at that exact moment.

This repository helps track those changes by keeping a historical record of OneSettings flight changes.

## Feature name resolution

Some OneSettings entries only expose a numeric Feature ID. Where possible, the monitor tries to resolve those IDs to readable feature names.

The reports can include fields such as:

```text
FeatureId
Feature name
Name source
Resolver source
Resolver reason
Source
PayloadKey
Options
Variant
Ct
Start
Expire
```

Feature names may be resolved through known Velocity feature data or local binary and symbol based fallback logic. When a name cannot be resolved, the report marks it as unresolved instead of guessing.

## How to read a change report

Each generated report starts with a change summary.

Example:

```text
# OneSettings flight changes
Changed UTC: 2026-07-03T04-00-04Z

## Change counts

| Change | Count |
| --- | ---: |
| FeatureConfig added | 1 |
```

Below the summary, the report shows the actual changes.

Example:

```text
FeatureId: 60496766
Feature name: CustomMetadataInvalidateUAFFix
Source: FeatureConfig.json
PayloadKey: PAYLOAD_62952015_1
```

This means a new FeatureConfig entry was detected for that run.

## What this is useful for

This repository can help with:

```text
Tracking Controlled Feature Rollout movement
Spotting newly added Windows feature IDs
Comparing OneSettings changes over time
Investigating why behavior changed without a new update
Understanding when Microsoft changes rollout configuration
Finding feature names behind raw Feature IDs
```

It is especially useful when researching Windows internals, Autopilot behavior, feature rollouts, servicing changes, or cases where a feature appears to be installed but not yet active.

## What this repository is not

This repository does not force enable Windows features.

It does not modify OneSettings payloads.

It does not bypass Microsoft rollout controls.

It is only a monitoring and research repository that records observed changes over time.

## Folder structure

```text
.
├── changes
│   ├── 2026-06-11T15-41-02Z.md
│   ├── 2026-06-13T08-28-57Z.md
│   ├── 2026-06-17T07-00-05Z.md
│   └── ...
├── LICENSE
└── README.md
```

## Known limitations

Feature names are not always available.

Some Feature IDs may stay unresolved until the name appears in public Velocity data, local binaries, symbols, or other resolvable sources.

A detected OneSettings change does not automatically mean the feature is enabled on every device. Rollout logic can still depend on build, region, flighting ring, device state, eligibility rules, experiment configuration, and Microsoft controlled targeting.

## Research purpose

This project is intended for research, troubleshooting, and visibility.

Use the data as a signal that something changed in OneSettings, not as proof that a feature is active on every Windows device.

## License

This repository is licensed under the MIT License.

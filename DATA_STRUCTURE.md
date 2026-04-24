# GameData Structure

This repository stores machine-readable snapshots exported from the current Kerbal Space Program save through kRPC.

The repository is generated data. Generated files may be overwritten by the exporter. The `manual-notes/` folder is reserved for human notes and must not be modified, deleted, staged, or committed by the exporter.

## Top-Level Files

- `README.md`: Short repository purpose and editing warning.
- `DATA_STRUCTURE.md`: This structure reference.
- `game-status.json`: Current save-level status, including timestamp, save name, funds, reputation, science, game scene, universal time, and active vessel reference.

## Contracts

- `contracts/active-contracts.json`: Active contracts visible through kRPC.
- `contracts/offered-contracts.json`: Offered contracts visible through kRPC.
- `contracts/completed-contracts.json`: Completed contracts visible through kRPC.

Contract entries include title, state, synopsis, description, notes, reward values, failure values, and nested objective parameters.

## Science

- `science/science-status.json`: Current science, funds, and reputation snapshot.
- `science/tech-tree.json`: Current unlocked part list derived from the save file.

## Vessels

- `vessels/active-vessel.json`: Active vessel telemetry and structure. Includes situation, stage, location, velocity, orbit, resources, and parts.
- `vessels/known-vessels.json`: Saved VAB craft names plus loaded vessels visible to kRPC.

## Parts

- `parts/unlocked-parts.json`: Parts unlocked in the current save, derived from the save file technology section.

## Resources

- `resources/current-resources.json`: Current active-vessel resources, including amount and maximum capacity for detected resources.

## Summaries

- `summaries/mission-summary.md`: Human-readable snapshot of funds, science, reputation, active vessel, and situation.
- `summaries/next-actions-context.md`: Human-readable planning context focused on the active vessel and resources.

## Export Behavior

Each export writes one or more generated files, then runs git add, commit, and push for generated paths only. If there are no changes, the exporter reports that nothing changed. If no git remote is configured, the exporter commits locally and reports that push was skipped.

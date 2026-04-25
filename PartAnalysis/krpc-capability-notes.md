# kRPC Capability Notes

Generated: 2026-04-25

## What Was Tested

This is a source and documentation investigation using the local kRPC repository, not a live KSP session test.

Files inspected:
- `krpc/service/SpaceCenter/src/Services/SpaceCenter.cs`
- `krpc/service/SpaceCenter/src/Services/Vessel.cs`
- `krpc/service/SpaceCenter/src/Services/Resources.cs`
- `krpc/service/SpaceCenter/src/Services/Resource.cs`
- `krpc/service/SpaceCenter/src/Services/Parts/Part.cs`
- `krpc/service/SpaceCenter/src/Services/Parts/Engine.cs`
- `krpc/core/src/Service/KRPC/KRPC.cs`
- `krpc/server/src/Utils/GameScenesExtensions.cs`

## What Works

- kRPC can detect the current game scene through `conn.krpc.current_game_scene`. The server maps KSP scenes to `SpaceCenter`, `Flight`, `TrackingStation`, `EditorVAB`, and `EditorSPH`.
- kRPC can list saved craft names from save-folder craft directories through `space_center.launchable_vessels("VAB")` and `space_center.launchable_vessels("SPH")`.
- kRPC can launch a named VAB craft to the launchpad through `space_center.launch_vessel_from_vab(name, recover=True)`.
- kRPC can launch a named SPH craft to the runway through `space_center.launch_vessel_from_sph(name, recover=True)`.
- kRPC can read active vessel parts, part names, titles, stages, decouple stages, wet mass, and dry mass once the vessel exists in the Flight scene, including pre-launch on the pad.
- kRPC can read vessel mass and dry mass in the Flight scene.
- kRPC can read resources in the Flight scene. `Resources.amount("SolidFuel")` and `Resources.max("SolidFuel")` can provide current amount and capacity.
- kRPC can read engine data in the Flight scene, including SRBs after the craft is on the pad: propellant names, fuel availability, available thrust, max thrust, thrust limit, throttle locked state, shutdown/restart capability, and thrust limiter.
- kRPC can recover vessels through `vessel.recover()` if the vessel is recoverable, and launch methods can recover existing pad/runway vessels when `recover=True`.
- kRPC can switch to the Space Center scene through `space_center.load_space_center()`.

## What Does Not Work

- No direct SpaceCenter API was found for reading the currently loaded editor craft from VAB/SPH as a `Vessel`.
- No direct SpaceCenter API was found for traversing `EditorLogic.fetch.ship` or returning editor parts as normal kRPC `Part` objects.
- No direct SpaceCenter API was found for reading full saved craft-file contents. kRPC lists craft names and internally loads templates for launch, but it does not expose those templates through an RPC.
- No direct API was found for setting or modifying resource amounts such as `SolidFuel` directly. `Resource.amount` is read-only in the service source.
- `Engine.thrust_limit` is read/write in source, but source comments and KSP behavior indicate setting it may have no effect for some engines, especially solid rocket boosters in flight. This must be measured live before relying on it.

## What Is Uncertain

- Whether `Engine.thrust_limit` can be changed for an SRB while the craft is pre-launch on the pad. The property setter exists, but the source explicitly warns that some engines, for example solid rocket boosters, may ignore thrust-limit changes in flight.
- Whether editor scene data could be accessed by adding a new kRPC service/plugin that wraps `EditorLogic.fetch.ship`. The current stock SpaceCenter service does not expose that path.
- Whether direct craft-file editing is acceptable operationally for mission generation. It is outside kRPC, but the local client can read/write `.craft` files if we choose that path later.

## Recommended MVP Path

Use fallback path 2: launchpad pre-flight analysis.

MVP:
- Launch or load the selected VAB craft to the launchpad.
- Read the active vessel in Flight/pre-launch.
- Export a `PartAnalysis` snapshot containing vessel mass, resource amounts/capacity, part masses, and engine fields.
- Treat this analysis as measurement of an existing active vessel, not as proof that the craft is legal to newly construct from unlocked inventory.

Do not build the MVP around direct editor craft inspection yet. Do not assume SRB thrust limiter edits work until live testing proves it.

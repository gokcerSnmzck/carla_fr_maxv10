# FR-maxV10 vehicle assets for CARLA

Git LFS-managed Unreal vehicle content for representing the Yuhesen FR-max Ackermann robot in the [CARLA–Autoware simulation project](https://github.com/gokcerSnmzck/autoware_carla_leaderboard_custom). The separate [carla_yuhesen_fr_max repository](https://github.com/gokcerSnmzck/carla_yuhesen_fr_max) describes vehicle geometry and sensors inside Autoware.

This repository's engineering role is packaging and integrating the robot into CARLA's vehicle system. The initial import by Gökcer Sönmezocak establishes asset publication; it does not establish original authorship of the CAD model, mesh, materials, or every Unreal asset.

## Asset contents

| Location under `CarlaUE4/Content/Carla/` | Contents |
|---|---|
| `Blueprints/Vehicles/FR-maxV10/` | Vehicle Blueprint and front-left/front-right/rear-left/rear-right wheel Blueprints, each with `.uasset`/`.uexp` payloads. |
| `Static/Vehicles/FR-maxV10/` | Robot mesh, skeleton, physics asset, animation asset, and aluminium/rubber/steel material assets. Despite the folder name, inspected mesh metadata identifies a **SkeletalMesh**. A separate static robot mesh was not established. |
| `Blueprints/` | Shared `VehicleFactory`, `BaseVehiclePawn`, `BaseVehiclePawnNW`, `CommonTireConfig` and `Wheel_Shape` assets. These affect the surrounding CARLA installation. |

There are **34 LFS-managed asset files** totaling **173,786,286 bytes**. All payloads were downloaded to a separate inspection directory and verified against pointer SHA-256 hashes and sizes during the portfolio review. The checkout may still contain pointer text until LFS retrieval is performed locally.

Binary inspection confirmed Unreal package signatures; Blueprint references to the robot mesh, wheel classes and animation; `SkeletalMesh`, `Skeleton` and `PhysicsAsset` metadata; and the string **`fr-maxv10`** in `VehicleFactory.uexp`. This supports the model's packaging and configured registration label. It does not validate mass, inertia, tire response, steering limits, visual quality, successful spawning, or compatibility with every CARLA build.

## Installation

The main project targets **CARLA 0.9.16 with native ROS 2 support**. Use the matching server/content build described in the [main setup guide](https://github.com/gokcerSnmzck/autoware_carla_leaderboard_custom/blob/main/docs/SETUP.md); packaged-server compatibility remains a runtime check.

```bash
git lfs install
git clone https://github.com/gokcerSnmzck/carla_fr_maxv10.git
cd carla_fr_maxv10
git lfs pull
git lfs fsck
```

Before installation, back up the destination content, especially the shared factory/base-pawn/tire files. Confirm that the Unreal/CARLA build can load these asset packages. A copy operation is deployment preparation, not an automated import/cook or compatibility guarantee.

```bash
export CARLA_ROOT=/path/to/CARLA_0.9.16
# Run from this repository root, after backing up the destination:
cp -a CarlaUE4/. "$CARLA_ROOT/CarlaUE4/"
cd "$CARLA_ROOT"
./CarlaUE4.sh --ros2
```

With the matching CARLA Python client, query the server before running an experiment:

```python
import carla
client = carla.Client("localhost", 2000)
client.set_timeout(10.0)
print("server:", client.get_server_version(), "client:", client.get_client_version())
print([bp.id for bp in client.get_world().get_blueprint_library().filter("*fr-max*")])
```

Use the returned full blueprint ID for spawning. The factory contains `fr-maxv10`, but the exact runtime ID still needs server confirmation; do not add a guessed prefix. If no blueprint is returned, inspect content loading, factory registration and build compatibility.

## Relationship to evaluation and control

The main repository sets `ego_vehicle_model: fr-maxv10`. However, its declared TUM Leaderboard dependency currently hard-codes an Audi ego actor and does not consume that setting. An adapted spawning dependency is required. Once a matching actor is spawned, the custom Python agents read its `type_id`; the substring `fr-max` activates robot-specific steering, longitudinal control and sensor definitions.

The mesh and physics assets do not supply the Autoware vehicle description or ROS 2 sensors. Obtain those from [carla_yuhesen_fr_max](https://github.com/gokcerSnmzck/carla_yuhesen_fr_max) and follow the [main architecture](https://github.com/gokcerSnmzck/autoware_carla_leaderboard_custom/blob/main/docs/ARCHITECTURE.md) and [setup prerequisites](https://github.com/gokcerSnmzck/autoware_carla_leaderboard_custom/blob/main/docs/SETUP.md#execution-prerequisites). Main-project documentation links become available when the portfolio changes are published.

No simulator run, successful model load, physics validation, screenshots or benchmark results are claimed by the payload inspection.

## Author and asset provenance

Packaging and integration: **Gökcer Sönmezocak**, M.Eng. Embedded Systems Engineering, FH Dortmund — University of Applied Sciences and Arts.

The repository does not currently provide an asset provenance statement or a root license file. Do not infer permission to redistribute third-party models or shared CARLA assets from the import commit. Preserve applicable upstream rights and establish asset provenance and licensing before wider redistribution. This model is a research simulation asset; it does not establish physical-platform safety.

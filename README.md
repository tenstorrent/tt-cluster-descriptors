# tt-cluster-descriptors

Static cluster descriptor files used by [tt-metal](https://github.com/tenstorrent/tt-metal) for multi-device testing and configuration.

## Overview

This repository contains YAML cluster descriptor files that describe multi-chip Tenstorrent hardware topologies. These descriptors are consumed by tt-metal's device management layer (and `tt-run`/mock-cluster tooling) to configure multi-chip routing and connectivity without requiring physical hardware.

tt-metal consumes this repo as a git submodule mounted at
`tt_metal/third_party/tt-cluster-descriptors`. The files were previously kept in
tt-metal under `tests/tt_metal/tt_fabric/custom_mock_cluster_descriptors/`.

## Layout

Descriptors are organized by architecture, with a separate section for superclusters
(multiple galaxies / pods networked together). **Each cluster lives in its own
directory.**

```
wormhole/                 Wormhole (WH) systems
blackhole/                Blackhole (BH) systems
superclusters/
  ├── wormhole/           WH multi-pod scaleout (closetbox superpod, …)
  └── blackhole/          BH multi-galaxy / multi-pod scaleout (SC16/SC20/SP3/SC4, …)
```

### Per-cluster directory structure

- **Multi-host cluster** — a directory named after the cluster, holding its
  `*_mapping.yaml` file(s) at the top and the per-host descriptor YAMLs in a
  `<name>_cluster_desc/` subdirectory:

  ```
  superclusters/blackhole/SC16_32x4_revAB_aisleD/
    SC16_32x4_revAB_aisleD_mapping.yaml
    SC16_32x4_revAB_aisleD_single_pod_mapping.yaml
    SC16_32x4_revAB_aisleD_dual_4x8_z_fallback_mapping.yaml
    SC16_32x4_revAB_aisleD_cluster_desc/        # 16 per-host descriptor YAMLs
  ```

  A single descriptor set can have **multiple mappings** that select different
  rank counts / subsets of the same hosts (e.g. the `SP3_16x8_revAB_aisleC` set is
  shared by `1pod`, `3pod`, `3pod_blitz`, and `blitz_z_fallback` mappings).

- **Single-host descriptor** — its own directory containing the single
  `*_cluster_desc.yaml` (plus any mapping that references it):

  ```
  wormhole/t3k_cluster_desc/
    t3k_cluster_desc.yaml
    t3k_2x4_big_mesh_cluster_desc_mapping.yaml
  ```

### Supercluster naming

```
<class>_<mesh>_<rev>_[subtorus_]aisle<C|D>
```

| Token | Meaning |
|-------|---------|
| `class` | `SC<hosts>` (SuperCluster by host count: `SC16`, `SC20`, `SC24`, `SC4`) or `SP<pods>` (`SP3` = 3-pod 16x8) |
| `mesh`  | logical mesh shape, e.g. `32x4`, `16x8` |
| `rev`   | hardware revision: `revAB` (current) or `revC` (rev-C boards; most are system-110 captures, but rev-C also appears on other host series such as `bg-ale22`) |
| `subtorus` | present for subtorus captures |
| `aisle`/location | physical aisle from the host-series letter (`c…` → `aisleC`, `d…` → `aisleD`), or a named lab/location for hosts outside the lettered aisles (e.g. `virtu` for the `bg-ale22` series) |

Supercluster sets:

| Cluster | Hosts | Notes |
|---------|-------|-------|
| `superclusters/blackhole/SP3_16x8_revAB_aisleC/` | 12 | shared by 1-pod & 3-pod mappings |
| `superclusters/blackhole/SC20_32x4_revAB_aisleC/` | 20 | |
| `superclusters/blackhole/SC24_32x4_revC_subtorus_virtu/` | 24 | Virtu SC24, subtorus connectivity, `bg-ale22` host series; shared by the full SC24 mapping and the SC20 decode-subset mapping (nodes 5–24) |
| `superclusters/blackhole/SC20_32x4_revC_subtorus_aisleC/` | 20 | system-110 subtorus, Aisle C; shared by SC20 / SC16 (excludes c07/c08) / SC4 (c07/c08 only) mappings |
| `superclusters/blackhole/SC16_32x4_revC_aisleC/` | 16 | system-110, Aisle C |
| `superclusters/blackhole/SC16_32x4_revAB_aisleD/` | 16 | |
| `superclusters/blackhole/SC16_32x4_revC_subtorus_aisleD/` | 16 | system-110 subtorus, Aisle D |
| `superclusters/wormhole/wh_closetbox/` | 16 | WH closetbox superpod |

## Path conventions (important)

Paths **inside** the `*_mapping.yaml` files are written relative to `TT_METAL_HOME`
(the tt-metal checkout root), e.g.:

```yaml
rank_to_cluster_mock_cluster_desc:
  0: "tt_metal/third_party/tt-cluster-descriptors/wormhole/6u_dual_host/6u_dual_host_cluster_desc/6u_dual_host_cluster_desc_rank_0.yaml"
```

This matches how tt-metal's `tt-run` (`ttrun.py`) and `rtoptions` resolve mock cluster
descriptors, so these mapping files are intended for use **from within a tt-metal
checkout** that has this repo mounted at the path above.

## Usage from tt-metal

- Single descriptor: set `TT_METAL_MOCK_CLUSTER_DESC_PATH` to a descriptor path, or pass
  a bare filename to the C++ `set_mock_cluster_desc(<filename>)` API — tt-metal searches
  this submodule tree (recursively) first, then falls back to UMD's
  `cluster_descriptor_examples/`.
- Multi-host mock: `tt-run --mock-cluster-rank-binding <…/some_mapping.yaml> …`.

## Generating new descriptors

To capture a descriptor from real hardware, obtain a `ClusterDescriptor` (e.g. via
`get_cluster()`) and call `serialize_to_file`. On multi-host systems this must be done
per host to capture every host's ethernet connectivity. Add the resulting files under
the matching arch (or `superclusters/`) directory in a new per-cluster directory,
following the naming scheme above.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## Security

For reporting security vulnerabilities, see [SECURITY.md](SECURITY.md).

## License

[Apache License 2.0](LICENSE) — see [LICENSE_understanding.txt](LICENSE_understanding.txt) for additional context.

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).

# tt-cluster-descriptors

Static cluster descriptor files used by [tt-metal](https://github.com/tenstorrent/tt-metal) for multi-device testing and configuration.

## Overview

This repository contains YAML cluster descriptor files that describe multi-chip Tenstorrent hardware topologies. These descriptors are consumed by tt-metal's device management layer to configure multi-chip routing and connectivity for various board configurations (T3000, TG, TGG, etc.).

## Usage

Cluster descriptor files are referenced by tt-metal test infrastructure and CI pipelines. They are not intended to be modified by end users — they represent physical hardware configurations.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

[Apache License 2.0](LICENSE) — see [LICENSE_understanding.txt](LICENSE_understanding.txt) for additional context.

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).

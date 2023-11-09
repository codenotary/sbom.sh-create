# sbom.sh-create
SBOM.sh container analysis and scan using Grype provided as a GitHub Action
# SBOM.sh GitHub Action

This GitHub Action integrates with [sbom.sh](https://sbom.sh/) to generate and upload Software Bill of Materials (SBOM) for your projects. Utilizing the `codenotary/sbom.sh` container image, this action supports various open-source SBOM tools such as Trivy, Grype, and Syft.

## Features

- Generate SBOMs for filesystems and container images.
- Support for multiple scanning tools (Trivy, Grype, Syft).
- Upload SBOMs directly to sbom.sh and obtain a shareable URL.
- Optional vulnerability scanning and SBOM score calculation at sbom.sh.

## Usage

To use this action, add the following workflow to your `.github/workflows` directory:

```yaml
name: "Generate and Upload SBOM"

on: [push, pull_request, workflow_dispatch]

jobs:
  generate_sbom:
    runs-on: ubuntu-latest
    name: "SBOM Generation"
    steps:
      # ... workflow steps here
```

Customize your workflow by specifying the scanning tool and target using the `workflow_dispatch` event.

## Inputs
- `scan_type`: Type of scan (trivyfs, trivyimage, grypefs, grypeimage, syftfs, syftimage).
- `target`: Scan target (applicable for image scans).

## About sbom.sh

sbom.sh is a service that allows you to generate a valid Software Bill-of-Materials (SBOM) in CycloneDX or SPDX format. It provides a centralized platform to store SBOMs and facilitates better tracking and analysis of your software components.

For detailed documentation on the sbom.sh service and GitHub Action, visit https://sbom.sh or https://hub.docker.com/repository/docker/codenotary/sbom.sh/general.

## License
This GitHub Action and corresponding scripts are released under the Apache2 License. See LICENSE for details.

## Contributing
Contributions are welcome!
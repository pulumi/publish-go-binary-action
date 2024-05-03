# Publish Go Binary GitHub Action

GitHub workflow for building Go binary artefacts for multiple platforms.

## Usage

```yaml
uses: ./.github/workflows/build-binaries.yml
with:
  # Name of the binary to build.
  # This input is required.
  binary-name: your-binary
  # Go packages to be built. Multiple packages can be specified, separated by spaces.
  # This input is required.
  packages: github.com/org/repo/cmd/your-binary
  # Semantic version of the release.
  # Must be valid semantic version 2.0 (without leading "v")
  # This input is required.
  version: '1.0.0'
  # Path to a version variable in the source code to set.
  # This input is required.
  version-path: github.com/org/repo/pkg/version.Version
  # Root directory of the go module.
  # Defaults to ${{ github.workspace }}
  root-dir: ${{ github.workspace }}
  # Optional bash command to run before the go build.
  pre-build: make ensure
  # Additional files to include in the package, separated by spaces
  extra-package-files: README.md LICENCE
  # GitHub runner to use for build job.
  # Default: ubuntu-latest-4-cores
  runs-on: ubuntu-latest
  # Set to '1' to enable CGO. See https://pkg.go.dev/cmd/cgo for more information.
  cgo-enabled: '0'
  # Additional command line argument to pass to `go build`.
  build-args: '-ldflags="-s -w"'
```

### Outputs

| Name | Description | Example |
| - | - | - |
| `assets-prefix` | Prefix used for all uploaded assets. | `your-binary_v1.2.3` |
| `checksums-filename` | Filename of the checksums file and asset. | `your-binary_v1.2.3_checksums.txt` |
| `linux-amd64-filename` | Filename of the linux-amd64 package. | `your-binary_v1.2.3-linux-amd64.tar.gz` |
| `linux-arm64-filename` | Filename of the linux-arm64 package. | `your-binary_v1.2.3-linux-arm64.tar.gz` |
| `darwin-amd64-filename` | Filename of the darwin-amd64 package. | `your-binary_v1.2.3-darwin-amd64.tar.gz` |
| `darwin-arm64-filename` | Filename of the darwin-arm64 package. | `your-binary_v1.2.3-darwin-arm64.tar.gz` |
| `windows-amd64-filename` | Filename of the windows-amd64 package. | `your-binary_v1.2.3-windows-amd64.tar.gz` |

## Examples

### Typical Pulumi Provider Build

```yaml
jobs:
  version:
    runs-on: ubuntu-latest
    name: Calculate version
    steps:
      - id: version
        uses: pulumi/provider-version-action@v1
    outputs:
      version: ${{ steps.version.outputs.version }}

  build:
    uses: pulumi/publish-go-binary-action/.github/workflows/build-binaries.yml@v1
    name: Build Go Binaries
    needs: version
    with:
      binary-name: pulumi-resource-xyz
      packages: github.com/pulumi/pulumi-xyz/provider/cmd/pulumi-resource-xyz
      version: ${{ needs.version.outputs.version }}
      version-path: github.com/pulumi/pulumi-xyz/provider/pkg/version.Version
      root-dir: provider
      extra-package-files: README.md LICENCE
```

<p align="right">
<a href="https://autorelease.general.dmz.palantir.tech/palantir/github-actions"><img src="https://img.shields.io/badge/Perform%20an-Autorelease-success.svg" alt="Autorelease"></a>
</p>

github-actions
==============
Hosts GitHub Actions that can be imported by other repositories.

go-toolchain-setup
------------------
Selects the Go toolchain declared by a module's `go.mod` file (or an explicitly specified version), acquires it using
Go's native toolchain management, and activates it for subsequent steps.

The toolchain is selected using the rules of the `github.com/palantir/pkg/gotoolchain` package:
1. If the `toolchain` directive is present and is not `default`, its value is used.
2. Otherwise, the `go` directive is used with a `go` prefix, appending `.0` to Major.Minor versions (`go 1.27` selects
   `go1.27.0`).

`go.work` files are not consulted. The selection logic is implemented by `go-toolchain-setup/gotoolchain.sh`, which is
distributed from the `gotoolchain` module and must not be modified in this repository.

The selected toolchain is acquired by running the bootstrap `go` executable on `PATH` with `GOTOOLCHAIN` set to the
selected version. This uses the bootstrap's bundled toolchain or a version-named executable on `PATH` if either matches,
and otherwise downloads the toolchain through the configured module proxy. A Go 1.21 or later `go` executable must
therefore be on `PATH` before this action runs. The action does not set `GOPROXY`, `GOSUMDB` or related variables: set
them in the job environment when downloads must go through a different proxy or checksum database.

After the action runs, the selected toolchain's `bin` directory is first on `PATH`, `GOROOT` is set to the selected
toolchain, and `GOTOOLCHAIN=local` and `GOWORK=off` are set so that subsequent Go commands do not switch toolchains.

Inputs:

| Input | Default | Description |
|---|---|---|
| `working-directory` | `.` | Directory containing the `go.mod` file that determines the toolchain. |
| `go-version` | `""` | Exact toolchain version to use instead of selecting from `go.mod` (for example `go1.27.3`). |
| `if-no-go-mod` | `fail` | Behavior when `go.mod` does not exist and `go-version` is empty: `fail`, or `skip` to set `found=false` and make no changes. |

Outputs:

| Output | Description |
|---|---|
| `found` | `true` if a toolchain was selected and activated, `false` if setup was skipped. |
| `version` | The selected toolchain version (for example `go1.27.3`). |
| `goroot` | The `GOROOT` of the activated toolchain. |

Example usage:

```
  - uses: actions/checkout@v4
  - id: go
    uses: palantir/github-actions/go-toolchain-setup@<version>
  - run: echo "Using ${{ steps.go.outputs.version }}"
```

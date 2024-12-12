<p align="right">
<a href="https://autorelease.general.dmz.palantir.tech/palantir/github-actions"><img src="https://img.shields.io/badge/Perform%20an-Autorelease-success.svg" alt="Autorelease"></a>
</p>

github-actions
==============
Hosts GitHub Actions that can be imported by other repositories.

go-dist-info
------------
Returns information about the Go distribution.

Example usage:

```
  - id: go-dist-info
    uses: palantir/godel/go-dist-info@0.1.0
    with:
      gopath: ${{ steps.set-gopath.outputs.GOPATH }}
      go-version: go1.23.3
```

go-dist-setup
-------------
Sets up a Go distribution.

Example usage:

```
  - id: go-dist-setup
    uses: palantir/godel/go-dist-setup@0.1.0
    with:
      gopath: ${{ steps.set-gopath.outputs.gopath }}
      go-version: ${{ steps.go-dist-info.outputs.go-dist-version }}
```

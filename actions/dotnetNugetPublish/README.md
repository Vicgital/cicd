# Publish NuGet Package

Packs a .NET project and publishes it to the Vicgital GitHub Packages NuGet feed.

## Usage

```yaml
name: Publish NuGet Package

on:
  push:
    branches: [ "main" ]

jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      packages: write
      contents: read
    steps:
      - uses: Vicgital/cicd/actions/dotnetNugetPublish@main
        with:
          startupProject: src/Vicgital.Core.Logging/Vicgital.Core.Logging.csproj
          githubPublishPackageToken: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Name | Required | Default | Description |
|---|---|---|---|
| `startupProject` | yes | - | Path to the `.csproj` file to pack |
| `githubPublishPackageToken` | yes | - | GitHub authentication token used to push the package, typically `secrets.GITHUB_TOKEN` |
| `dotnetVersion` | no | `10.0.x` | .NET SDK version to use |
| `skipCheckout` | no | `false` | Skip the checkout step, e.g. if the code is already checked out at the workflow level or in a previous job |
| `fetchDepth` | no | `1` | Number of commits to fetch (only used if the checkout step isn't skipped) |

## What it does

1. Checks out the caller repository (`actions/checkout@v4`) using `fetchDepth`, unless
   `skipCheckout` is `true`.
2. Sets up the .NET SDK (`actions/setup-dotnet@v4`) using `dotnetVersion`.
3. Runs `dotnet pack` on `startupProject` in `Release` configuration, output to `./nupkg`, using
   `githubPackageUsername`/`githubPackageToken` (as `GH_PACKAGE_USERNAME`/`GH_PACKAGE_TOKEN`) to
   restore any dependencies from GitHub Packages.
4. Pushes the resulting `.nupkg` to `https://nuget.pkg.github.com/vicgital/index.json` via
   `dotnet nuget push`, authenticating with `githubPublishPackageToken` and skipping duplicates.

## Notes

- Secrets aren't implicitly available to composite actions, so the calling workflow must pass
  them in explicitly as inputs (`githubPublishPackageToken`).
- The calling workflow's job needs `permissions: packages: write` for the push step to succeed.
- The NuGet feed URL is hardcoded to the Vicgital GitHub Packages org and isn't configurable
  as an input.

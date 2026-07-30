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
      - uses: actions/checkout@v4

      - uses: Vicgital/cicd/actions/dotnetNugetPublish@main
        with:
          startupProject: src/Vicgital.Core.Logging/Vicgital.Core.Logging.csproj
```

## Inputs

| Name | Required | Default | Description |
|---|---|---|---|
| `startupProject` | yes | - | Path to the `.csproj` file to pack |
| `dotnetVersion` | no | `10.0.x` | .NET SDK version to use |

## What it does

1. Sets up the .NET SDK (`actions/setup-dotnet@v4`) using `dotnetVersion`.
2. Runs `dotnet pack` on `startupProject` in `Release` configuration, output to `./nupkg`.
3. Pushes the resulting `.nupkg` to `https://nuget.pkg.github.com/vicgital/index.json` via
   `dotnet nuget push`, skipping duplicates.

## Notes

- The push step authenticates with `secrets.GITHUB_TOKEN`. The calling workflow's job needs
  `permissions: packages: write` for this to succeed.
- The NuGet feed URL is hardcoded to the Vicgital GitHub Packages org and isn't configurable
  as an input.

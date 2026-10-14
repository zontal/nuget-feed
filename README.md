# ZONTAL NuGet feed

A static NuGet v3 feed of the packages built by ZONTAL's patched forks. It is served by GitHub Pages and needs no
credentials:

```
https://zontal.github.io/nuget-feed/index.json
```

The same packages are published to GitHub Packages (`https://nuget.pkg.github.com/zontal/index.json`), which
requires a token with `read:packages` even for public packages.

| Fork | Packages | Version |
| --- | --- | --- |
| [zontal/UA-.NETStandard](https://github.com/zontal/UA-.NETStandard) | `OPCFoundation.NetStandard.Opc.Ua.*` | `2.0.0-zontal.*` |
| [zontal/raft-cs](https://github.com/zontal/raft-cs) | `RaftCs`, `RaftCs.*` | `1.2.0-zontal.*` |

## Use the feed

Map the fork's package ids to this feed and everything else to nuget.org, so a package id is only ever resolved from
one source:

```xml
<configuration>
  <packageSources>
    <clear />
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" />
    <add key="zontal" value="https://zontal.github.io/nuget-feed/index.json" />
  </packageSources>
  <packageSourceMapping>
    <packageSource key="zontal">
      <package pattern="OPCFoundation.NetStandard.Opc.Ua.*" />
      <package pattern="RaftCs" />
      <package pattern="RaftCs.*" />
    </packageSource>
    <packageSource key="nuget.org">
      <package pattern="*" />
    </packageSource>
  </packageSourceMapping>
</configuration>
```

## How packages get here

The feed is written only by the `ZONTAL pack` workflow of each fork, through the reusable workflow
[`.github/workflows/publish.yml`](.github/workflows/publish.yml):

1. [Sleet](https://github.com/emgarten/Sleet) pushes the run's packages into `docs/` (configured in
   [`sleet.json`](sleet.json)) and the commit is pushed with the deploy key held by the fork as the
   `NUGET_FEED_DEPLOY_KEY` secret.
2. GitHub Pages deploys `docs/`.
3. The workflow restores every pushed package from the public URL without credentials.

Do not edit `docs/` by hand.

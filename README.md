# Csag Code Analysis rulesets
We maintain two (opinionated) rulesets for all our projects and distribute them, including 3rd-party Roslyn Analyzers (StyleCop and SonarSource) in two respective NuGet packages.

- For Libraries, Tools, Web, UI and other projects
  - [Csag.CodeAnalysis](https://www.nuget.org/packages/Csag.CodeAnalysis/)
- For projects containing automated tests (less strict rules)
  - [Csag.CodeAnalysis.TestsRuleset](https://www.nuget.org/packages/Csag.CodeAnalysis.TestsRuleset/)

Up to version 3.2.2 the packages were published as `Neolution.CodeAnalysis` and `Neolution.CodeAnalysis.TestsRuleset`. Version 3.2.2 has the same rules under both IDs, so moving to the new packages means replacing the package ID and keeping the version. The [CHANGELOG](https://github.com/collana-solutions/Csag.CodeAnalysis/blob/main/Csag.CodeAnalysis/CHANGELOG.md) describes the migration.

## Decisions and opinions
The base ruleset consists of the default ruleset of the Roslyn Analyzers we include in the NuGet package and those used in our build tools.

- We include our own opinionated ruleset with adjusted Analyzer severities as a [Global AnalyzerConfig](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/configuration-files#global-analyzerconfig)
- In all build configurations other that `Debug`, warnings will be converted to errors.
- We include our own [stylecop.json](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/Configuration.md) configuration file.

## Versioning
Given a version number `MAJOR.MINOR.PATCH`, an incremented number for:

- `MAJOR` version means there are incompatible changes with the previous version,
- `MINOR` version means functionality and rules were added, and
- `PATCH` version means that the changes are all completely backwards compatible. Usually when rules were lowered in severity or disabled/removed.

### What it means in practice:
- You can (and should) always update the package to the latest `PATCH` version whenever you have the chance to do it, it should never break the build. 
- Updating to the latest `MINOR` version can break the build and may require minor refactorings. But you can expect to have Roslyn code fixes and/or documentation available for the changes that are needed to fix the build.
-  `MAJOR` updates will break your build and may require major refactorings.

## Preview packages
Each pull request to `main` or to a `release/**` branch publishes both packages to a public preview feed on feedz.io. A draft PR, a PR from a fork and a PR from Dependabot publish no preview. The version is one patch above the current release, for example `3.2.3-pr.42.183` (PR 42, workflow run 183). The **"chore: version packages"** PR publishes a release candidate of the upcoming version instead, for example `3.3.0-pr.73.190`, so the changes of the next release can be tested before it ships. In prerelease mode the version in `package.json` is a prerelease such as `4.0.0-rc.0`, and the preview extends it, for example `4.0.0-rc.0.pr.42.183`. That version sorts after the prerelease and before the next one. A comment on the PR gives the version of its latest push. The feed removes previews after 30 days. Releases go to nuget.org only.

To use a preview in an application, add the feed to the `nuget.config` of the application. The feed needs no credentials:

```xml
<packageSources>
  <clear />
  <add key="nuget.org" value="https://api.nuget.org/v3/index.json" />
  <add key="csag-codeanalysis-previews" value="https://f.feedz.io/collana-solutions/csag-codeanalysis/nuget/index.json" />
</packageSources>
<packageSourceMapping>
  <packageSource key="nuget.org">
    <package pattern="*" />
    <package pattern="Csag.CodeAnalysis*" />
  </packageSource>
  <packageSource key="csag-codeanalysis-previews">
    <package pattern="Csag.CodeAnalysis*" />
  </packageSource>
</packageSourceMapping>
```

The `Csag.CodeAnalysis*` pattern is also necessary under nuget.org. NuGet looks for a package only in the sources of its most specific pattern, so without it the released versions do not restore.

To test a preview:

1. Set `Csag.CodeAnalysis` and `Csag.CodeAnalysis.TestsRuleset` to the preview version.
2. If the application restores in locked mode, update its `packages.lock.json` files.
3. Add the feed only on the branch that tests the preview. The main branch of the application then does not need feedz.io to restore.

## Release process
This repository follows the [collana-solutions release playbook](https://github.com/collana-solutions/release-playbook) (Changesets, NuGet variant):

1. Every PR that changes a package needs a changeset. Run `pnpm changeset`, pick the packages the change affects, choose the bump type following the [versioning rules](#versioning) above and describe the change for consumers of the packages. The two packages are a fixed group, so they always share one version. CI's **Changeset Check** fails a PR that changes one of the project folders, `Directory.Build.props`, `Directory.Packages.props` or `global.json` without a changeset. Add an empty one (`pnpm changeset --empty`) when such a change ships nothing. A PR that only touches CI or repository documentation needs none.
2. On merge to `main`, the Release workflow keeps a **"chore: version packages"** PR up to date. It bumps the versions in the `package.json` files, writes them into the `.csproj` files with `scripts/sync-versions.mjs` and updates the `CHANGELOG.md` files.
3. Merging that PR creates the git tags and the GitHub Releases. The release of `Csag.CodeAnalysis` starts the **NuGet Publish** workflow, which packs both packages and pushes them to nuget.org through trusted publishing.

Each project directory holds what its package ships: the `.nuspec`, which also sets the versions of the StyleCop and SonarSource analyzers, and the `build/` folder with the props and the rules. `Csag.CodeAnalysis.TestsRuleset` also packs the base rules and the `stylecop.json` of `Csag.CodeAnalysis`. To build the packages locally, run `dotnet pack --configuration Release --output ./nupkgs`. Do not edit the `CHANGELOG.md` files or the `<Version>` in the `.csproj` files by hand.

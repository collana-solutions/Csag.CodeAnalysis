# @collana-solutions/csag-code-analysis-tests-ruleset

## 3.2.2

The packages move to new IDs. Version 3.2.2 has the same rules, build settings and analyzer dependencies as version 3.2.2 under the old IDs.

| Up to 3.2.2 | From 3.2.2 |
| --- | --- |
| `Neolution.CodeAnalysis` | `Csag.CodeAnalysis` |
| `Neolution.CodeAnalysis.TestsRuleset` | `Csag.CodeAnalysis.TestsRuleset` |

**To migrate,** replace the package ID in the package reference or in `Directory.Packages.props` and keep the version. Nothing else changes: the build properties, the diagnostic IDs and severities, the `global_level` of the rule configuration and the `stylecop.json` settings are the same, so overrides in `.editorconfig` or `.globalconfig` files keep working. In a project with `packages.config`, uninstall the old package and install the new one with the NuGet package manager, which also updates the `<Import>` of the build props.

Switch all projects of a repository at once, for example in `Directory.Build.props`. A project that gets both an old and a new package receives every rule setting twice. The compiler then warns with `MultipleGlobalAnalyzerKeys` and drops these settings, so the rules fall back to the analyzers' defaults.

`Neolution.CodeAnalysis` and `Neolution.CodeAnalysis.TestsRuleset` get deprecated on nuget.org, with the new IDs as alternates. Their existing versions stay available, but they get no new releases.

## History of Neolution.CodeAnalysis.TestsRuleset

The changes up to 3.2.2 are in the [changelog of Csag.CodeAnalysis](../Csag.CodeAnalysis/CHANGELOG.md#history-of-neolutioncodeanalysis), together with those of `Neolution.CodeAnalysis`. Entries marked "[Tests ruleset]" apply to this package only.

# @collana-solutions/csag-codeanalysis-testsruleset

## 3.3.0

### Minor Changes

- [#38](https://github.com/collana-solutions/Csag.CodeAnalysis/pull/38) [`6805a21`](https://github.com/collana-solutions/Csag.CodeAnalysis/commit/6805a21d4aa7149ff4c6a0e52cb753388984f341) Thanks [@neoscie](https://github.com/neoscie)! - Update SonarAnalyzer.CSharp from 9.20.0.85982 to 9.32.0.97167 and change the severity of some rules.
  
  Version 3.3.0 has the same rules as `Neolution.CodeAnalysis` 3.3.0-beta.2. The tests ruleset also sets SA1615 and S3900 to `none`, as in 3.2.2. To replace the beta, change the package ID and set the version to 3.3.0. Change all projects of a repository at the same time, as for 3.2.2.
  
  The new Sonar rules can add warnings to your build. In all build configurations other than `Debug`, warnings are errors, so the build can fail.
  
  **Changed rules**
  
  - S6931: ASP.NET controller actions should not have a route template starting with "/". Now `none`.
  - S6678: Use PascalCase for named placeholders. Now `suggestion`.
  - S6964: Value type property used as input in a controller action should be nullable, required or annotated with the JsonRequiredAttribute to avoid under-posting. Now `none`.
  - S2139: Exceptions should be either logged or rethrown but not both. Now `suggestion`.
  
  **Rules that SonarAnalyzer.CSharp 9.32 adds**
  
  - S2629: Logging templates should be constant
  - S3431: "[ExpectedException]" should not be used
  - S4347: Secure random number generators should not output predictable values
  - S5344: Passwords should not be stored in plaintext or with a fast hashing algorithm
  - S6377: XML signatures should be validated securely
  - S6667: Logging in a catch clause should pass the caught exception as a parameter.
  - S6668: Logging arguments should be passed to the correct parameter
  - S6670: "Trace.Write" and "Trace.WriteLine" should not be used
  - S6672: Generic logger injection should match enclosing type
  - S6673: Log message template placeholders should be in the right order
  - S6674: Log message template should be syntactically correct
  - S6675: "Trace.WriteLineIf" should not be used with "TraceSwitch" levels
  - S6677: Message template placeholders should be unique
  - S6781: JWT secret keys should not be disclosed
  - S6930: Backslash should be avoided in route templates
  - S6932: Use model binding instead of reading raw request data
  - S6934: A Route attribute should be added to the controller when a route template is specified at the action level
  - S6960: Controllers should not have mixed responsibilities
  - S6961: API Controllers should derive from ControllerBase instead of Controller
  - S6962: You should pool HTTP connections with HttpClientFactory
  - S6965: REST API actions should be annotated with an HTTP verb attribute
  - S6966: Awaitable method should be used
  - S6967: ModelState.IsValid should be called in controller actions
  - S6968: Actions that return a value should be annotated with ProducesResponseTypeAttribute containing the return type
  
  **Rules that SonarAnalyzer.CSharp 9.32 removes**
  
  - S6803: Parameters with SupplyParameterFromQuery attribute should be used only in routable components

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

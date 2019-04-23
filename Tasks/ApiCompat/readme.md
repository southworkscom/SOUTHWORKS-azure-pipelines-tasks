This is an Azure Pipelines encapsulation of the ApiCompat tool. To get the lastest version of this tool please visit [dotnet/arcade](https://github.com/dotnet/arcade/tree/master/src/Microsoft.DotNet.ApiCompat) repo. We do not own the original tool, we just created this task to have an easy way to use it on Azure Pipelines.

# ApiCompat task

API Compat tool compares two sets of assemblies and determines if the implementation has incompatibilities with the contract.

## How to configure it

#### Contracts root folder

Path to the folder that contains the assemblies to compare *against*.

#### Contracts files name

Space separated list of the names of the assemblies to compare to.

#### Implementation assemblies folder

Folder that contains the assemblies that are going to be compared against the contracts.

#### Fails on Issue
Checking this will cause the build process to fail when ApiCompat finds one or more incompatibilities. Leaving it unchecked will flag the task as *successful with issues*. For Pull Requests we recommend to leave this checked to alert to alert the Pull Request user that his implementations caused incompatibilities.

#### Resolve Fx

If a contract or implementation dependency cannot be found in the given directories, fallback to try to resolve against the framework directory on the machine.

#### Warn on incorrect version

Warn if the contract version number doesn't match the found implementation version number.

#### Warn on missing assemblies

Warn if the contract assembly cannot be found in the implementation directories. Default is to error and not do analysis.

#### Create Comparison Result Log

If this is checked, a JSON file will be created with the comparison result.

The JSON will contain two properties:

- `issues`. This property contains the numeric value of the amount of found issues.
- `body`. This property contains the list of descriptions of the issues given by ApiCompat.

Checking this will also show two additional input boxes:

- **Output File Name.** Name and extension of the file to generate.
- **Output File Path.** Path where the generated file will be stored.

#### Use Baseline File

A baseline file is a file that contains known incompatibilities. This come useful whenever one want to release a new major version of the assembly, knowing beforehand that the new version will contain breaking incompatibilities.

The content of this file is simply the same output generated by ApiCompat, either by piping the result to a text file or by using the `--out` parameter.

When running ApiCompat with this file as optional parameter with `--baseline`, ApiCompat will ignore the issues that are in the baseline file.

Checking this option will make appear a input field to select a baseline file, and it will be fed as parameter to ApiCompat when running the comparison.

## Basic Example

If you have a build process for your assemblies, this tool can be very useful to prevent the integration of new changes that will cause incompatibilities with the current released version of your assemblies.

To do this in your build process, add this task and configure the following fields:

- **Contracts root folder.** Point this to the latest released version of your assembles. One way accomplish this is to use the NuGet task.
- **Contracts files name.** Configure the name of your assembly. If you wish to configure more than one assembly, use *multipliers variables*.
- **Implementation assemblies folder.** Point this field to the binaries produced by the *current build process*.
- **Fails on Issues.** Check this if you wish to prevent incompatibilities.

For the build trigger configure  **Pull request validation**.

With this configuration, whenever a person opens a Pull Request introducing changes that will break compatibilities with the current released version, the build process will be marked as ***Failed***.

## More Information

- [Multi-Configuration](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml#multi-configuration).To configure multiple assemblies and versions without having to clone the build.
- [Conditional Tasks](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/conditions?view=azure-devops&tabs=yaml). To execute certain agents only if some conditions are met.

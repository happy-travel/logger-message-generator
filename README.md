High performance log delegates generator tool. Uses LogEvents.json from project to generate ILogger extensions.

The solution is implemented as a dotnet tool, more information at https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools

#### Install steps (for installing from sources):
1. Open command prompt and navigate to project folder
2. Run commands:

`dotnet pack`

`dotnet tool install LoggerMessageGenerator -g --add-source ./nupkg`

#### Uninstall steps:
1. Open command prompt
2. Run command

`dotnet tool uninstall LoggerMessageGenerator -g`

#### How to use:
1. Add `LogEvents.json` file to the project folder, in which `LoggerExtensions.g.cs` should be generated. 
2. Fill file contents using an example at https://github.com/happy-travel/LoggerMessageGenerator/blob/master/LoggerMessageGenerator/Example/LogEvents.json
3. Open command prompt and navigate to target project folder
4. Run `generate-loggermessages` command

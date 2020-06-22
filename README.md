#### Summary
Log delegates generator tool. Uses LogEvents.json from project to generate ILogger extensions for high performance logging https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/loggermessage?view=aspnetcore-3.1

The solution is implemented as a dotnet tool, more information at https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools

#### Install steps (for installing from sources, for external use):
1. Clone the repository, open command prompt and navigate to target project folder (where tool should be executed)
2. Run commands:

`dotnet pack`

`dotnet tool install LoggerMessageGenerator -g --add-source ./nupkg`


#### Install steps (for installing github packages, internal use):
1. Run command prompt and navigate to target project (where log events should be generated)
2. Run command
`dotnet tool install LoggerMessageGenerator -g`

#### Uninstall steps:
1. Open command prompt
2. Run command

`dotnet tool uninstall LoggerMessageGenerator -g`

#### How to use:
1. Add `LogEvents.json` file to the project folder, in which `LoggerExtensions.g.cs` should be generated. 
2. Fill file contents using an example at https://github.com/happy-travel/logger-message-generator/blob/master/LoggerMessageGenerator/Example/LogEvents.json
3. Open command prompt and navigate to target project folder
4. Run `generate-logmessages` command


#### LogEvents.json format:
The file can contain two event types: exceptions and not exceptionspo. To add an event use the following syntax:

 `{"id": <%EventId%>, "name": "<%EventTitle%>", "level": "<%LogLevel%>", "source": "<%EventSource%>", "isException": <%IsException%>}`

Where:
- **EventId** - is a numerical value, unique Id of event
- **EventTitle** - is a textual name of the event
- **LogLevel** - event logging level: Information, Debug, Error etc. from https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.loglevel?view=dotnet-plat-ext-3.1
- **EventSource** - typically a class or area name where event is occured, in a textual form,
- **IsException** - flag indicating whether this event is exception or textual log

Depending on the values of event fields, logger generator will create logger messages with logger extensions method. For example, for LogEvents.json with:

```
[
    {"id": 1001, "name": "CustomerRegistrationException", "level": "Critical", "source": "CustomerService", "isException": true},
    {"id": 1002, "name": "CustomerRegistrationSuccess", "level": "Information", "source": "CustomerService", "isException": false},
]
```

the following file will be generated:

```
    internal static class LoggerExtensions
    {
        static LoggerExtensions()
        {
            CustomerRegistrationExceptionOccured = LoggerMessage.Define(LogLevel.Critical,
                new EventId(1001, "CustomerRegistrationException"),
                $"CRITICAL | CustomerService: ");
            
            CustomerRegistrationSuccessOccured = LoggerMessage.Define<string>(LogLevel.Information,
                new EventId(1002, "CustomerRegistrationSuccess"),
                $"INFORMATION | CustomerService: {{message}}");
            
        }
    
                
         internal static void LogCustomerRegistrationException(this ILogger logger, Exception exception)
            => CustomerRegistrationExceptionOccured(logger, exception);
                
         internal static void LogCustomerRegistrationSuccess(this ILogger logger, string message)
            => CustomerRegistrationSuccessOccured(logger, message, null);
    
    
        
        private static readonly Action<ILogger, Exception> CustomerRegistrationExceptionOccured;
        
        private static readonly Action<ILogger, string, Exception> CustomerRegistrationSuccessOccured;
    }
 ```

 
To use this from code just inject ILogger to class and call `_logger.CustomerRegistrationSuccessOccured("Customer was registered succrssfully")

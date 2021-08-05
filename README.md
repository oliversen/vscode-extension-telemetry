# [vscode-extension-telemetry](https://www.npmjs.com/package/vscode-extension-telemetry)
This module provides a consistent way for first-party extensions to report telemetry
over Application Insights. The module respects the user's decision about whether or
not to send telemetry data.

Follow [guide to set up Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-nodejs-quick-start) in Azure and get your key.

# Install
`npm install vscode-extension-telemetry`

# Usage

## Setup
```javascript
import * as vscode from 'vscode';
import TelemetryReporter from 'vscode-extension-telemetry';

// all events will be prefixed with this event name
const extensionId = '<your extension unique name>';

// extension version will be reported as a property with each event
const extensionVersion = '<your extension version>';

// the application insights key (also known as instrumentation key)
const key = '<your key>';

// telemetry reporter
let reporter;

function activate(context: vscode.ExtensionContext) {
   // create telemetry reporter on extension activation
   reporter = new TelemetryReporter(extensionId, extensionVersion, key);
   // ensure it gets properly disposed. Upon disposal the events will be flushed
   context.subscriptions.push(reporter);
}
```

### First-Party

By default, we use the AppInsights key to detect whether or not the telemetry is first-party. The constructor now takes an optional parameter that will force the reporter to treat telemetry as first-party. This parameter will not override in the false direction.

## Sending Events

Use this method for sending general events to App Insights.

```javascript
// send event any time after activation
reporter.sendTelemetryEvent('sampleEvent', { 'stringProp': 'some string' }, { 'numericMeasure': 123 });
```

## Sending Exceptions

Use this method for diagnostics in App Insights. This method will automatically drop events in certain environments for first party extensions.

```javascript
// send an error any time after activation
try { ... } catch (error) {
   reporter.sendTelemetryException(error, { 'stringProp': 'some string' }, { 'numericMeasure': 123 });
}
```

## Sending Errors as Events

Use this method for sending error telemetry as traditional events to App Insights. This method will automatically drop error properties in certain environments for first party extensions. The last parameter is an optional list of case-sensitive properties that should be dropped. If no array is passed, we will drop all properties but still send the event.

```javascript
// send an error event any time after activation
reporter.sendTelemetryErrorEvent('sampleErrorEvent', { 'stringProp': 'some string', 'stackProp': 'some user stack trace' }, { 'numericMeasure': 123 }, [ 'stackProp' ]);
```


# Common Properties
- **Extension Name** `common.extname` - The extension name
- **Extension Version** `common.extversion` - The extension version
- **Machine Identifier** `common.vscodemachineid` - A common machine identifier generated by VS Code
- **Session Identifier** `common.vscodesessionid` - A session identifier generated by VS Code
- **VS Code Version** `common.vscodeversion` - The version of VS Code running the extension
- **OS** `common.os` - The OS running VS Code
- **Platform Version** `common.platformversion` - The version of the OS/Platform
- **UI Kind** `common.uikind` - Web or Desktop indicating where VS Code is running
- **Remote Name** `common.remotename` - A name to identify the type of remote connection. `other` indicates a remote connection not from the 3 main extensions (ssh, docker, wsl).

# License
[MIT](LICENSE)

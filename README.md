# azure-app-service
Notes about continuous deployment using Azure App Service

## Node.js version

Until `package.json` will have [`engines.node`][engines] path, App Service would use LTS version, but not the latest one. E.g. at February 8, 2016 the latest LTS release was 4.2.6, while App Services had 4.2.3.

[engines]: https://docs.npmjs.com/files/package.json#engines

```
remote: The package.json file does not specify node.js engine version constraints.        
remote: The node.js application will run with the default node.js version 4.2.3.        
remote: Selected npm version 3.5.1
```

There is an _Application Setting_ called `WEBSITE_NODE_DEFAULT_VERSION` which was set to 4.2.3 (on February 8, 2016) by default.

There is no support for ranges, you could set only explicit version:

```
remote: Node.js versions available on the platform are: 0.6.20, 0.8.2, 0.8.19, 0.8.26, 0.8.27, 0.8.28, 0.10.5, 0.10.18, 0.10.21, 0.10.24, 0.10.26, 0.10.28, 0.10.29, 0.10.31, 0.10.32, 0.10.40, 0.12.0, 0.12.2, 0.12.3, 0.12.6, 4.0.0, 4.1.0, 4.1.2, 4.2.1, 4.2.2, 4.2.3, 4.2.4, 5.0.0, 5.1.1, 5.3.0, 5.4.0, 5.5.0.
remote: No available node.js version matches application's version constraint of '^5.5.0'. Use package.json to choose one of the available versions. 
```

When used `5.5.*`

```
remote: Node.js versions available on the platform are: 0.6.20, 0.8.2, 0.8.19, 0.8.26, 0.8.27, 0.8.28, 0.10.5, 0.10.18, 0.10.21, 0.10.24, 0.10.26, 0.10.28, 0.10.29, 0.10.31, 0.10.32, 0.10.40, 0.12.0, 0.12.2, 0.12.3, 0.12.6, 4.0.0, 4.1.0, 4.1.2, 4.2.1, 4.2.2, 4.2.3, 4.2.4, 5.0.0, 5.1.1, 5.3.0, 5.4.0, 5.5.0.        
remote: Selected node.js version 5.5.0. Use package.json file to choose a different version.
```

## Start

```
remote: Looking for app.js/server.js under site root.        
remote: Using start-up script app.js 
...
remote: Invalid start-up command "rejoice -c app.js" in package.json. Please use the format "node <script relative path>".
```

## `rejoice` and non-node `start` scripts

```
remote: Invalid start-up command "rejoice -c app.js" in package.json. Please use the format "node <script relative path>".
```

Changing to `{ start: "node_modules/.bin/rejoice -c app.js" }` would not fix issue, but change error message to 

```
remote: Start script "node_modules/.bin/rejoice -c app.js" from package.json is not found.
```

So we need separate `start` script:

```json
{ "start": "node server.js" }
```

`server.js`:

```js
'use strict';

const Rejoice = require('rejoice');

Rejoice.start({ args: ['node', 'rejoice', '-c', 'app.js'] });

```

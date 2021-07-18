Firebase Cheatsheet
===

## Functions

### set
```shell-script
# environment variables
## set
$ firebase functions:config:set someservice.key="THE API KEY" someservice.id="THE CLIENT ID"
```

### get in shell
```shell-script
firebase functions:config:get
# {
#   "someservice": {
#     "key":"THE API KEY",
#     "id":"THE CLIENT ID"
#   }
# }
```

### get in js

```js
const functions = require('firebase-functions');
console.log(functions.config().someservice.key);
```

### dump to local (for run emulator)

```shell-script
firebase functions:config:get > .runtimeconfig.json
```

Repro of what seems like a Meteor issue when using `debugOnly` packages via the new `api.mainModule`
exporting API.

Previously we could do something like this to reference debug-only packages,
which may or may not be defined:
```
OLD package.js
--------------
Package.describe({
  name: 'debug-only-package',
  debugOnly: true
});
...
api.export('DebugOnlyFoo');
```

Now we try to do this:
```
NEW package.js
---------------
Package.describe({
  name: 'debug-only-package',
  debugOnly: true
});
...
api.mainModule('main.js');

with main.js
----------
export const DebugOnlyFoo = 'bar';
```

The old way of referencing things inside debug-only packages was something like:
```
const DebugOnlyFooPackage = Package['debug-only-package'];

// DebugOnlyFoo may or may not be something useful...
if (DebugOnlyFooPackage) {
  ...
}
```

However with the new modules API:
```
import { DebugOnlyFoo } from 'meteor/debug-only-package';
```

when running in production mode (`meteor --production`),
notice that there is an error in the JS console like:
```
Uncaught Error: Cannot find module 'meteor/debug-only-package'
```

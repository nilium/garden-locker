garden-locker
=============

This is an attempt to produce a repository that does roughly the same things
as an internal Garden project, but making everything as close to a null op as
possible so it can be used for debugging.

As-is, this project fails both validation and deployments with the output shown
below (under _Failure Output_). This can be made to succeed by removing one
pair of modules, in this case using `build-5` and `service-5` as the example,
from the `modules` directory. See the _Success Output_ heading for that output.

This depends on <https://github.com/nilium/dummy-redis>, which absolutely
should not be used for anything outside of this test project (because it's
bad). Its sole purpose is to reproduce the errors I'm getting below.

## Failure Output

In all cases, it is seemingly random which module is reported in the
error. With silly output, the fuller error is reported as follows, though this
likely isn't much use due to how shallow the trace is:

```
Failed resolving one or more modules:

build-5: Lock file is already being held
[silly] build-5: Error: Lock file is already being held
    at /snapshot/project/tmp/pkg/cli/node_modules/proper-lockfile/lib/lockfile.js:68:47
    at callback (/snapshot/project/tmp/pkg/cli/node_modules/graceful-fs/polyfills.js:306:20)
    at FSReqCallback.oncomplete (node:fs:212:5)
    at FSReqCallback.callbackTrampoline (node:internal/async_hooks:130:17)

Error Details:

build-5:
  code: ELOCKED
  file: /home/ncower/p/garden-locker/.garden/local-config.json
```

### garden validate
```
# garden validate
Validate

ℹ garden               → Running in Garden environment rancher.sandbox
ℹ providers            → Getting status...
✔ providers            → Cached (took 1.2 sec)
ℹ providers            → Run with --force-refresh to force a refresh of provider statuses.
ℹ graph                → Resolving actions and modules...


Failed resolving one or more modules:

service-6: Lock file is already being held

See .garden/error.log for detailed error message
ℹ build-5              → Fetching from ssh://git@github.com/nilium/dummy-redis#main
```

### garden deploy 
```
# garden deploy service-7
Deploy

ℹ garden               → Running in Garden environment rancher.sandbox
ℹ providers            → Getting status...
✔ providers            → Cached (took 1.3 sec)
ℹ providers            → Run with --force-refresh to force a refresh of provider statuses.
ℹ graph                → Resolving actions and modules...


Failed resolving one or more modules:

service-6: Lock file is already being held

See .garden/error.log for detailed error message
```

## Success Output

The following output is from after moving build-5 and service-5 out of the
modules/ directory, at least temporarily.

```
# mv modules/{build,service}-5 .

(Undo)
# mv {build,service}-5 modules/
```

### garden deploy

```
# garden deploy service-7
Deploy

ℹ garden               → Running in Garden environment rancher.sandbox
ℹ providers            → Getting status...
✔ providers            → Cached (took 1.3 sec)
ℹ providers            → Run with --force-refresh to force a refresh of provider statuses.
ℹ graph                → Resolving actions and modules...
✔ graph                → Done (took 31.6 sec)
ℹ deploy.service-7     → missing
ℹ deploy.service-7     → Deploying version v-67a3b797e7...
ℹ deploy.service-7     → Waiting for resources to be ready...
ℹ deploy.service-7     → Resources ready
✔ deploy.service-7     → Done (took 14.9 sec)

Done!
```

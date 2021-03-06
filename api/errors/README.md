Docker 'errors' package
=======================

This package contains all of the error messages generated by the Docker
engine that might be exposed via the Docker engine's REST API.

Each top-level engine package will have its own file in this directory
so that there's a clear grouping of errors, instead of just one big
file. The errors for each package are defined here instead of within
their respective package structure so that Docker CLI code that may need
to import these error definition files will not need to know or understand
the engine's package/directory structure. In other words, all they should
need to do is import `.../docker/api/errors` and they will automatically
pick up all Docker engine defined errors.  This also gives the engine
developers the freedom to change the engine packaging structure (e.g. to
CRUD packages) without worrying about breaking existing clients.

These errors are defined using the 'errcode' package. The `errcode`  package
allows for each error to be typed and include all information necessary to
have further processing done on them if necessary.  In particular, each error
includes:

* Value - a unique string (in all caps) associated with this error.
Typically, this string is the same name as the variable name of the error
(w/o the `ErrorCode` text) but in all caps.

* Message - the human readable sentence that will be displayed for this
error. It can contain '%s' substitutions that allows for the code generating
the error to specify values that will be inserted in the string prior to
being displayed to the end-user. The `WithArgs()` function can be used to
specify the insertion strings.  Note, the evaluation of the strings will be
done at the time `WithArgs()` is called.

* Description - additional human readable text to further explain the
circumstances of the error situation.

* HTTPStatusCode - when the error is returned back to a CLI, this value
will be used to populate the HTTP status code. If not present the default
value will be `StatusInternalServerError`, 500.

Not all errors generated within the engine's executable will be propagated
back to the engine's API layer. For example, it is expected that errors
generated by vendored code (under `docker/vendor`) and packaged code
(under `docker/pkg`) will be converted into errors defined by this package.

When processing an errcode error, if you are looking for a particular
error then you can do something like:

```
import derr "github.com/docker/docker/api/errors"

...

err := someFunc()
if err.ErrorCode() == derr.ErrorCodeNoSuchContainer {
	...
}
```

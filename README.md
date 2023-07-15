# PythonMonkey

![Logo](https://i.imgur.com/znyErBj.png)

![Testing Suite](https://github.com/Kings-Distributed-Systems/PythonMonkey/actions/workflows/tests.yaml/badge.svg)

## About
PythonMonkey is a package for Python programming language that allows using JavaScript code from Python and vice versa. It is named after Mozilla [SpiderMonkey](https://firefox-source-docs.mozilla.org/js/index.html) JavaScript engine that this package embeds into the [Python VM](https://leanpub.com/insidethepythonvirtualmachine/read), thereby providing a JS host environment within Python.

<!--The input on the wording would be more than welcome. -->

## Examples

Example of a Python script that evaluates the JavaScript expression and prints it:

```python
#!/usr/bin/env python3
"""
This example returns a "HELLO, WORLD!" string created in JavaScript
using `pm.eval` for execting the JavaScript code.
The string is set to uppercase using JavaScript's `toUpperCase`
function.
"""

import pythonmonkey as pm
hello = pm.eval(" 'Hello, World!'.toUpperCase(); ")
print(hello) # this outputs "HELLO, WORLD!"
```

* More basic examples can be found in the [`/examples`](https://github.com/Distributive-Network/PythonMonkey/tree/main/examples) directory.
* Slightly more advanced examples can be found in the dedicated repository <https://github.com/Distributive-Network/PythonMonkey-examples>.
* Example of a fullstack [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) encryption and decryption app that uses the [`crypto-js`](https://www.npmjs.com/package/crypto-js) [NPM](https://en.wikipedia.org/wiki/Npm) package can be found at <https://github.com/Distributive-Network/PythonMonkey-Crypto-JS-Fullstack-Example>

<!--It would be amazing to have a [Binder](https://jupyter.org/binder) repo with interactive examples -->

## Product maturity

This product is in an early stage, approximately 90% to [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) as of July 2023. It is under active development by Distributive Corp.,
<https://distributive.network/>. External contributions and feedback are welcome and encouraged.

## Goal of the package

The goal is to make writing code in either JS or Python a developer preference rather than a hard requirement, with libraries commonly used in either language available everywhere, with no significant data exchange or transformation penalties. For example, it should be possible to use [NumPy](https://numpy.org/) methods from a JS library, or to refactor a slow ["hot loop"](https://en.wikipedia.org/wiki/Hot_spot_(computer_programming)) written in Python to execute in JS instead, taking advantage of  SpiderMonkey's JIT for near-native speed, rather than writing a C-language module for Python. At Distributive, we intend to use  this package to execute our complex [`dcp-client`](https://github.com/Distributed-Compute-Labs/dcp-client) library, which is written in JS and enables [distributed computing](https://www.geeksforgeeks.org/what-is-distributed-computing/) on the web stack.

### Data Interchange
- Strings share immutable backing stores whenever possible (when allocating engine choses [UCS-2](https://en.wikipedia.org/wiki/Universal_Coded_Character_Set) or [Latin-1 (aka ISO/IEC 8859-1)](https://en.wikipedia.org/wiki/ISO/IEC_8859-1) internal string representation) to keep memory consumption under control, and to make it possible to move very large strings between JS and Python library code without memory-copy overhead.
- JS [`TypedArray`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)s to share mutable backing stores; if this is not possible we will implement a [copy-on-write (CoW)](https://en.wikipedia.org/wiki/Copy-on-write) solution.
- [JS objects](https://www.w3schools.com/js/js_objects.asp) are represented by [Python dictionarires](https://www.w3schools.com/python/python_dictionaries.asp).
- [JS `Date`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) objects are represented by [Python `datetime.datetime` objects](https://docs.python.org/3/library/datetime.html#available-types:~:text=and%20tzinfo.-,class%20datetime.datetime,-A%20combination%20of).
- JS intrinsics ([`boolean`](https://developer.mozilla.org/en-US/docs/Glossary/Primitive#:~:text=bigint-,boolean,-undefined), [`number`](https://developer.mozilla.org/en-US/docs/Glossary/Number), [`null`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/null), [`undefined`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)) are passed by value.
- JS Functions are automatically wrapped so that they behave like Python functions, and vice versa.

### Roadmap
- [x] JS instrinsics coerce to Python intrinsics
- [x] JS strings coerce to Python strings
- [x] JS objects coerce to Python dicts \[own-properties only\]
- [x] JS functions coerce to Python function wrappers
- [x] JS exceptions propagate to Python
- [x] Implement `eval()` function in Python which accepts JS code and returns JS->Python coerced values
- [x] NodeJS+NPM-compatible CommonJS module system
- [x] Python strings coerce to JS strings
- [x] Python intrinsics coerce to JS intrinsics
- [x] Python dicts coerce to JS objects
- [x] Python `require` function, returns a coerced dict of module exports
- [x] Python functions coerce to JS function wrappers
- [x] CommonJS module system .py loader, loads Python modules for use by JS
- [ ] JS object->Python dict coercion supports inherited-property lookup (via __getattribute__?)
- [x] Python host environment supplies event loop, including EventEmitter, setTimeout, etc.
- [ ] Python host environment supplies XMLHttpRequest (other project?)
- [ ] Python host environment supplies basic subsets of NodeJS's fs, path, process, etc, modules; as-needed by dcp-client (other project?)
- [x] Python TypedArrays coerce to JS TypeArrays
- [x] JS TypedArrays coerce to Python TypeArrays

## Build Instructions

Read this if you want to build a local version.

1. You will need the following installed (which can be done automatically by running `./setup.sh`):
    - bash
    - cmake
    - doxygen 
    - graphviz
    - llvm
    - rust
    - python3.8 or later with header files (python3-dev)
    - spidermonkey 102.2.0 or later
    - npm (nodejs)
    - [Poetry](https://python-poetry.org/docs/#installation)
    - [poetry-dynamic-versioning](https://github.com/mtkennerly/poetry-dynamic-versioning)

2. Run `poetry install`. This command automatically compiles the project and installs the project as well as dependencies into the poetry virtualenv.

If you are using VSCode, you can just press <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>B</kbd> to [run build task](https://code.visualstudio.com/docs/editor/tasks#_custom-tasks) - We have [the `tasks.json` file configured for you](.vscode/tasks.json).

## Running tests
1. Compile the project 
2. Install development dependencies: `poetry install --no-root --only=dev`
3. From the root directory, run `poetry run pytest ./tests/python`

For VSCode users, similar to the Build Task, we have a Test Task ready to use.

## Using the library

### Install from [PyPI](https://pypi.org/project/pythonmonkey/)

> PythonMonkey is not release-ready yet. Our first public release is scheduled for mid-June 2023.

```bash
$ pip install pythonmonkey
```

### Install the [nightly build](https://nightly.pythonmonkey.io/)

```bash
$ pip install --extra-index-url https://nightly.pythonmonkey.io/ --pre pythonmonkey
```

### Use local version

`pythonmonkey` is available in the poetry virtualenv once you compiled the project using poetry.

```bash
$ poetry run python
```
```py
Python 3.10.6 (main, Nov 14 2022, 16:10:14) [GCC 11.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import pythonmonkey as pm
>>> hello = pm.eval("() => {return 'Hello from Spidermonkey!'}")
>>> hello()
'Hello from Spidermonkey!'
```

Alternatively, you can build a `wheel` package by running `poetry build --format=wheel`, and install it by `pip install dist/*.whl`.

## Debugging Steps

1. [build the project locally](#build-instructions)
2. To use gdb, run `poetry run gdb python`.  
See [Python Wiki: DebuggingWithGdb](https://wiki.python.org/moin/DebuggingWithGdb)

If you are using VSCode, it's more convenient to debug in [VSCode's built-in debugger](https://code.visualstudio.com/docs/editor/debugging). Simply press <kbd>F5</kbd> on an open Python to start debugging - We have [the `launch.json` file configured for you](.vscode/launch.json).

## API
These methods are exported from the pythonmonkey module.

### require(moduleIdentifier)
Return the exports of a CommonJS module identified by `moduleIdentifier`, using standard CommonJS
semantics
 - modules are singletons and will never be loaded or evaluated more than once
 - moduleIdentifier is relative to the Python file invoking `require`
 - moduleIdentifier should not include a file extension
 - moduleIdentifiers which do not begin with ./, ../, or / are resolved by search require.path
   and module.paths.
 - Modules are evaluated immediately after loading
 - Modules are not loaded until they are required
 - The following extensions are supported:
 ** `.js` - JavaScript module; source code decorates `exports` object
 ** `.py` - Python module; source code decorates `exports` dict
 ** `.json` -- JSON module; exports are the result of parsing the JSON text in the file

### globalThis
A Python Dict which is equivalent to the globalThis object in JavaScript.

### createRequire(filename, extraPaths, isMain)
Factory function which returns a new require function
- filename: the pathname of the module that this require function could be used for
- extraPaths: [optional] a list of extra paths to search to resolve non-relative and non-absolute module identifiers
- isMain: [optional] True if the require function is being created for a main module

### runProgramModule(filename, argv, extraPaths)
Load and evaluate a program (main) module. Program modules must be written in JavaScript. Program modules are not
necessary unless the main entry point of your program is written in JavaScript.
- filename: the location of the JavaScript source code
- argv: the program's argument vector
- extraPaths: [optional] a list of extra paths to search to resolve non-relative and non-absolute module identifiers

Care should be taken to ensure that only one program module is run per JS context.

## Built-In Functions
- `console`
- `setTimeout`
- `setInterval`
- `clearTimeout`
- `clearInterval`

### CommonJS Subsystem Additions
The CommonJS subsystem is activated by invoking the `require` or `createRequire` exports of the (Python)
pythonmonkey module.
- `require`
- `exports`
- `module`
- `python.print`  - the Python print function
- `python.getenv` - the Python getenv function
- `python.stdout` - an object with `read` and `write` methods, which read and write to stdout
- `python.stderr` - an object with `read` and `write` methods, which read and write to stderr
- `python.exec`   - the Python exec function
- `python.eval`   - the Python eval function
- `python.exit`   - the Python exit function (wrapped to return BigInt in place of number)
- `python.paths`  - the Python sys.paths list (currently a copy; will become an Array-like reflection)

## Type Transfer (Coercion / Wrapping)
When sending variables from Python into JavaScript, PythonMonkey will intelligently coerce or wrap your
variables based on their type. PythonMonkey will share backing stores (use the same memory) for ctypes,
typed arrays, and strings; moving these types across the language barrier is extremely fast because
there is no copying involved.

*Note:* There are plans in Python 3.12 (PEP 623) to change the internal string representation so that
        every character in the string uses four bytes of memory. This will break fast string transfers
        for PythonMonkey, as it relies on the memory layout being the same in Python and JavaScript. As
        of this writing (July 2023), "classic" Python strings still work in the 3.12 beta releases.

Where shared backing store is not possible, PythonMonkey will automatically emit wrappers that use
the "real" data structure as its value authority. Only immutable intrinsics are copied. This means
that if you update an object in JavaScript, the corresponding Dict in Python will be updated, etc.

| Python Type | JavaScript Type |
|:------------|:----------------|
| String      | string
| Integer     | number
| Bool        | boolean
| Function    | function
| Dict        | object
| List        | Array-like object
| datetime    | Date object
| awaitable   | Promise
| Error       | Error object
| Buffer      | ArrayBuffer

| JavaScript Type      | Python Type     |
|:---------------------|:----------------|
| string               | String
| number               | Float
| bigint               | Integer
| boolean              | Bool
| function             | Function
| object - most        | JSObjectProxy which inherits from Dict
| object - Date        | datetime
| object - Array       | List
| object - Promise     | awaitable
| object - ArrayBuffer | Buffer
| object - type arrays | Buffer
| object - Error       | Error

## Tricks
### Integer Type Coercion
You can force a number in JavaScript to be coerced as an integer by casting it to BigInt.
```javascript
function myFunction(a, b) {
  const result = calculate(a, b);
  return BigInt(Math.floor(result));
}
```

### Symbol injection via cross-language IIFE
You can use a JavaScript IIFE to create a scope in which you can inject Python symbols:
```python
globalThis.python.exit = pm.eval("""'use strict';
(exit) => function pythonExitWrapper(exitCode) {
  if (typeof exitCode === 'number')
    exitCode = BigInt(Math.floor(exitCode));
  exit(exitCode);
}
""")(sys.exit);
```

# Troubleshooting Tips

## REPL - pmjs 
A basic JavaScript shell, `pmjs`, ships with PythonMonkey. This shell can also run JavaScript programs with 

## CommonJS (require)
If you are having trouble with the CommonJS require function, set environment variable DEBUG='ctx-module*' and you can see the filenames it tries to laod.

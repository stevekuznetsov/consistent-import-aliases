# consistent-import-aliases
Automation to ensure that consistent import aliases are used for packages across a Go project.

Often, projects will develop implicit rules and patterns around aliasing imports of packages with generic names.
As these patterns become more defined and ingrained, developers learn mental short-cuts between a package and its
preferred import alias; inconsistent import aliases lead to confusion and make a codebase less cohesive.

This tool will identify packages with inconsistent import aliases and do its best to rewrite source code to be consistent.

## Usage

### Analyzing Existing Source Code

Running in "dump mode" will analyze existing source code in a directory and emit a summary of inconsistent aliases:

```shell
$ consistent-import-aliases --root-dir my/project --dump-dir .
INFO[0000] wrote summary to import-aliases.config.go    
```

The new `import-aliases.config.go` summarizes the current state of your import aliases, and will be used as a configuration
file for this tool in re-write mode. The file will contain the most common import aliases for every package, when multiple
are present in your source code. You'll need to filter this down to one alias option per import path before using this file
to re-write source code.

```go

package main

import (
	"crypto/rand" /* used 2 times */
	cryptorand "crypto/rand" /* used 1 time */

	"flag" /* used 6 times */
	goflags "flag" /* used 4 times */
	goflag "flag" /* used 1 time */
)
```

NOTE: generated source code files are not included in the analysis of import aliases.
NOTE: side-effect imports are ignored in the analysis.

### Re-writing Source Code

Running in "re-write" mode will attempt to use the configured import alias (if any) for each module, across your codebase.
The tool expects a configuration file which is simply a Go source file with an `import` stanza. For every imported package
in the stanza, the tool will attempt to re-write all imports of that package to use the alias in the configuration file.

```shell
$ consistent-import-aliases --root-dir my/project --config import-aliases.config.go
```

NOTE: generated source code files are not edited by this tool.
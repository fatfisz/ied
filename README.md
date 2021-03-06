ied (WIP)
=========

[![Travis](https://img.shields.io/travis/alexanderGugel/ied.svg)](https://travis-ci.org/alexanderGugel/ied)
[![npm](https://img.shields.io/npm/v/ied.svg)](https://www.npmjs.com/package/ied)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](http://standardjs.com/)
[![Join the chat at https://gitter.im/migme/beachball](https://img.shields.io/badge/gitter-join%20chat-brightgreen.svg)](https://gitter.im/alexanderGugel/ied)

An alternative package manager for Node.

* **Concurrent Installations** - Sub-dependencies are being installed in parallel. This means that e.g. the download of a dependency might be completed before the installation of its respective parent or additional dependencies.
* **Correct Caching** - Downloaded packages are being cached locally. Similarly to the entry dependencies stored in `node_modules`, they are being identified by their checksums. Therefore we can guarantee the consistency of the cache itself without (manually) invalidating dependencies (e.g. due to overridden version numbers).
* **`node_modules` as CAS** - Packages are always being referenced by their *SHA-1* checksums. Therefore a `node_modules` directory can be considered to be a [Content Addressable Storage](https://en.wikipedia.org/wiki/Content-addressable_storage), meaning that packages are being identified by their contents, not by arbitrary identifiers, such as package names that are not guaranteed to be unique across different registries. This also means that packages don't need to be explicitly declared as `peerDependencies`.
* **Flat `node_modules`** - Due to the *CAS*-based design, conflicts due to naming collisions are impossible in practice. Therefore all dependencies can be stored in a flat directory structure. Circular dependencies and dependencies on different versions of the same packages are still being handled correctly.
* **Guaranteed uniqueness** - Since the directory in which a specific package is being stored is being determined by its *shasum*, identical packages can't conflict due to their location in the file system itself. This also means that the same dependency won't be installed more than once.
* **Atomic installs** - The atomicity of installs can be ensured on a *package-level*. "In progress" downloads are being stored in `node_modules/.tmp` and moved into `node_modules` once their download has been completed. In order to prevent deadlocks, packages that have circular dependencies are exempt from this limitation. In most cases however, the `node_modules` directory is *consistent* at any given point in time.
* **Package names as links** - While packages are being referenced by their *shasum* internally, they can still be required via their human-readable name. Package names themselves are simply symbolic links to the actual content-addressed package itself. A nice side-effect of this design is that in contrast to other package managers, you can not accidentally require a sub-dependency that hasn't been installed as such.
* **Semantic Versioning** - [Semantic versions](http://semver.org/) are being resolved correctly.
* **Arbitrary package groups** - Packages can be grouped into "package groups", such as `dependencies` and `devDependencies`. Dependencies can be installed exclusively based on the group they are in.

Why?
----

The original idea was to implement npm's pre-v3 install algorithm in as few lines as possible. This goal was achieved in [`c4ba56f`](https://github.com/alexanderGugel/ied/tree/c4ba56f7dece738db5b8cb28c20c7f6aa1e64d1d).

Currently the main goal of this project is to provide a more performant alternative to npm.


Installation
------------

The easiest way to install ied is using [npm](https://www.npmjs.org/):

```
  npm i -g ied
```

Alternatively you can also "bootstrap" ied.
After an initial installation via npm, ied will install its own dependencies:

```
  git clone https://github.com/alexanderGugel/ied ied && cd $_ && make install
```

Usage
-----

The goal of `ied` is to support ~ 80 per cent of the npm commands that one uses on a daily basis. Feature parity with npm **other than** with its installation process itself is not an immediate goal. Raw performance is the primary concern during the development process.

A global [configuration](lib/config.js) can be supplied via environment variables. `NODE_DEBUG` can be used in order to debug specific sub-systems. The progress bar will be disabled in that case.

Although `run-script` is supported, lifecycle scripts are not.

At this point in time, the majority of the command API is [self-documenting](bin/cmd.js). More extensive documentation will be available once the API is stabilized.

A high-level [USAGE](bin/USAGE.txt) help is also supplied. The main goal is to keep the API predictable for regular npm-users. This means certain flags, such as for example `--save`, `--save-dev`, `--only`, are supported.

```
  ied is a package manager for Node.

  Usage:

    ied command [arguments]

  The commands are:

    install     fetch packages and dependencies
    run         run a package.json script
    test        run the test-suite of the current package
    shell       enter a sub-shell with augmented PATH
    ping        check if the registry is up
    ls          print the dependency graph
    expose      make a sub-dependency `require`able
    config      print the used config
    init        initialize a new package
    link        link the current package or into it
    unlink      unlink the current package or from it

  Flags:
    -h, --help      show usage information
    -v, --version   print the current version
    -S, --save      update package.json dependencies
    -D, --save-dev  update package.json devDependencies
    -o, --only      install a subset of the dependencies
    -r, --registry  use a custom registry (default: http://registry.npmjs.org/)

  Example:
    ied install
    ied install <pkg>
    ied install <pkg>@<version>
    ied install <pkg>@<version range>

    Can specify one or more: ied install semver@^5.0.1 tape
    If no argument is supplied, installs dependencies from package.json.
    Sub-commands can also be called via their shorthand aliases.

  README:  https://github.com/alexanderGugel/ied
  ISSUES:  https://github.com/alexanderGugel/ied/issues
```

Credits
-------

Some ideas and (upcoming) features of ied are heavily inspired by [**Nix**](http://nixos.org/nix/), a purely functional package manager.

FAQ
---

* What does ied stand for?

  Nothing in particular. It's just easy to type and `mpm` (the original name) was already taken.

License
-------

Licensed under the MIT license. See [LICENSE](LICENSE.md).

RFC 75 meeting 1

RFC as is covers:

1. Declarative wrappers
2. Wrapping in separate derivation

Different use cases:
1. Composition (Python env) / configuration (firefox, plugins) using new derivation
2. Adding required dependencies of a derivation

Standard interface. Both are covered in RFC to consider and avoid double-wrapping.

Avoid double wrapping through smarter makeWrapper? makeWrapper with language-specific hooks to inject header with configuration (e.g. sys.path in case of Python).

Structured attributes are not yet available in stdenv in Nixpkgs. There are work-arounds, by passing a JSON file with your configuration to your derivation.

During build time the (structured) attributes of dependencies are not available.

## Recursion

How to compose? We need to recurse into dependencies to find what to put together.
We typically use setup hooks. When to stop recursing? For example, with Python packages we do not want
to mix different Python environments that exist in the transitive closure.

During eval-time ([as done for Python](https://github.com/NixOS/nixpkgs/pull/102613) with `requiredPythonModules`)  is expensive. How about writing "evaluated dependencies" as JSON to `$out/nix-support`?

There is also the idea of binary wrappers that solves the issue of nested shebangs on Darwin. They could also use a declarative interface. [Possible implementation](https://github.com/NixOS/nixpkgs/pull/95569). Maybe the same JSON instruction. Maybe not create a new binary wrapper for every executable but reuse it like is done with setuid?

Current proposal mixes the declaring of what to compose with how the composition is often implemented. That is, environment variables are used as attributes. While technically this works, often you want something higher-level, "this is a Qt" package, "this is a Python package". 


## Next step?

Think of a declarative interface to `makeWrapper`.

Take several packages, add to it the `envInputs` and the variables they need and write it to a JSON file. Then write a script that during build-time will wrap the target using these JSON files.

Update the RFC to describe this approach.

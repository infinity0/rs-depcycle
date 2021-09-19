# What's this?

This crate demonstrates a crate-level cyclic dependency in Cargo.

Cargo enforces acyclicity on the feature-level not the crate-level [1], and
therefore it is trivially possible to create cyclic dependencies on the
crate-level.

This is an instance of the general theorem that when you have a partial order
defined on a set B, and a non-injective surjective mapping F from that set to
another strictly smaller set S, then an ordering on S defined as

~~~~
s0 <= s1 iff exists b0 <= b1 where F(b0) = s0 and F(b1) = s1
~~~~

is not always a partial order, but is a pre-order that may contain cycles.

(This is related to various impossibility results on voting systems.)

## Consequences

As applied to package managers:

- B is "the set of crate features" or "the set of binary packages"
- the ordering on B is the feature-level or binary-level dependency ordering
- S is "the set of crates" or "the set of source packages"
- the ordering on S is the crate-level or source-level dependency ordering

Cargo does not use the ordering on S, and therefore these cycles do not have
any practical negative consequences on the Rust ecosystem. What cargo does, is
use the ordering on B to resolve feature-level dependencies, then translate the
chosen elements of B to determine the chosen elements of S - i.e. the crates
that need to be downloaded to satisfy feature-level dependencies.

However, naive ways of translating Cargo crates into other package managers may
result in the ordering on S being used by the other package manager. This has
the following practical negative consequences:

- the other package manager may not support cyclic dependencies on S

- even if it supports cyclic dependencies on S in the general case, in a
  specific case S may contain crates that conflict with each other. This would
  not be the case if the ordering on B were used directly, as conflicts on this
  level are self-policed away by Rust developers; but there is no such
  self-policing on the level of S.

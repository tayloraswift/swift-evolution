# Allow `@retroactive` to be applied to module-local protocols that imply external conformances 

* Proposal: SE-NNNN
* Author: [Diana Ma](https://github.com/tayloraswift)
* Review Manager: TBD
* Status: Pitch

## Introduction

Currently, when a protocol defined in the current module refines external protocols, conforming an external type to this local protocol requires spelling out each implied protocol conformance at the conformance site with the `@retroactive` attribute applied to the name of each implied protocol. Protocol composition syntax (e.g., `P & Q`) cannot be used to abbreviate these conformances. This proposal seeks to allow a single `@retroactive` attribute applied to a conformance to a local protocol to imply `@retroactive` conformances to all its external implied protocols.

## Motivation

The current requirement to enumerate all external protocol conformances individually with `@retroactive` is verbose and scales multiplicatively with the number of implied protocols and conformed types. Allowing a single `@retroactive` on the name of the local protocol would streamline such retroactive conformances and make files with large lists of conforming types (“conformance pages”) more compact and expressive.

## Proposed Solution

In this section, *module* may refer to a collection of modules treated as a resilence unit (e.g., can access `package`-visible declarations).

We would permit attaching `@retroactive` to an unconditional conformance to a protocol defined in the same module, if that protocol refines protocols defined outside the module. The compiler would recognize that conforming to the local protocol necessarily implies conformances to the external protocols, and would treat the attribute as if it were applied to each of the implied external protocols. 

## Detailed Design

- The compiler will accept a single `@retroactive` attribute on a conformance to a protocol defined in the current module, if that protocol refines external protocols.
- The conformance will be treated as retroactive for all implied external protocols.
- The `@retroactive` attribute is only valid when the local protocol’s inheritance tree includes protocols external to the module.
- Only unconditional conformances are eligible for this syntax.

### Example

**Today:**

```swift
extension ExternalType: @retroactive ExternalProtocol1, @retroactive ExternalProtocol2, LocalProtocol {}
```
Each external protocol must be listed with `@retroactive`.

**With this proposal:**

```swift
extension ExternalType: @retroactive LocalProtocol {}
```

Where `LocalProtocol` (defined in this module) refines `ExternalProtocol1` and `ExternalProtocol2`.

```swift
public protocol LocalProtocol: ExternalProtocol1, ExternalProtocol2 {}
```

## Impact on Existing Code

This is an additive change and does not affect existing code. 

## Alternatives Considered

### Status Quo

Continue requiring explicit retroactive conformances for each external protocol. 

### Protocol Composition with @retroactive

Require spelling out individual implied conformances, but allow a single `@retroactive` attribute to be applied to an entire protocol composition clause.

## Acknowledgments

Thanks to everyone who contributed feedback and motivation for this proposal.

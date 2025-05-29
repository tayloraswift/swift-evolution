# Proposal: Allow Module-Local Protocols to Abbreviate Retroactive Conformance with Protocol Composition Syntax

* Proposal: SE-NNNN
* Author: Diana Ma
* Review Manager: TBD
* Status: Pitch

## Introduction

Currently, when a protocol defined in a module ("module-local protocol") inherits or refines protocols declared outside the module, conforming an external type to this local protocol in a retroactive manner requires explicitly spelling out each external protocol conformance with the `@retroactive` attribute at the conformance site. Protocol composition syntax (e.g., `SomeProto & AnotherProto`) cannot be used to abbreviate these conformances. This proposal seeks to allow a single `@retroactive` attribute on a local protocol to suffice, as long as conforming to it implies all required retroactive conformances.

## Motivation

The current requirement to enumerate all external protocol conformances individually with `@retroactive` is verbose and error-prone. It also leads to boilerplate and obscures the relationship expressed by the module-local protocol, whose role is often to bundle a set of requirements (including external ones) under a meaningful name. Allowing a single `@retroactive` on the local protocol would streamline such retroactive conformances and make code more maintainable and expressive.

## Proposed Solution

Permit attaching `@retroactive` to a conformance for a protocol defined in the same module, even if that protocol refines or inherits protocols defined outside the module. The compiler would recognize that conforming to the local protocol necessarily implies conformances to the external protocols, and would treat the conformance as retroactive for all such protocols.

### Example

**Today:**
```swift
@retroactive extension ExternalType: ExternalProto1, ExternalProto2, LocalProto {}
```
Each external protocol must be listed with `@retroactive`.

**With this proposal:**
```swift
@retroactive extension ExternalType: LocalProto {}
```
Where `LocalProto` (defined in this module) refines `ExternalProto1` and `ExternalProto2`, all required retroactive conformances are implied.

## Detailed Design

- The compiler will accept a single `@retroactive` attribute on a conformance to a protocol defined in the current module, even if that protocol refines/depends on external protocols.
- The conformance will be treated as retroactive for all implied external protocols.
- The rule applies only when the local protocol's inheritance tree includes protocols external to the module.

## Impact on Existing Code

This is an additive change and does not affect existing valid code. It makes certain retroactive conformances more concise and expressive.

## Alternatives Considered

- **Status Quo:** Continue requiring explicit retroactive conformances for each external protocol. This is more verbose and less maintainable.
- **Generalized Protocol Composition with @retroactive:** Allow protocol composition syntax (e.g., `@retroactive extension T: P1 & P2 {}`) to abbreviate multiple retroactive conformances. This is a broader change, but the current proposal focuses on module-local protocols for clarity and minimal impact.

## Future Directions

This approach could be extended to support protocol composition syntax directly with `@retroactive` in the future.

## Acknowledgments

Thanks to everyone who contributed feedback and motivation for this proposal.

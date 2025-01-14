---
CIP: ????
Title: Structured Contracts
Category: Ledger/Tools
Status: Active
Authors:
  - Polina Vinogradova <polina.vinogradova@iohk.io>
Implementors: N/A
Discussions:
Created: 2024-04-15
License: Apache-2.0
---

## Abstract

This CIP describes a model of stateful computation called [structured contracts](https://omelkonian.github.io/data/publications/eutxo-struc.pdf) integrated with the Agda ledger model. The structured contract framework can be instantiated with smart contracts specified as labelled transition systems and implemented as Agda functions, together a proof obligation to show the integrity of the implementation. This framework unifies the idea of contract verification across multiple approaches to EUTxO programming by defining a minimum requirement for contract correctness to be the _correctness of the evolution of certain aspects of the ledger state which the implementing scripts of this contract control_. Basic illustrative examples are given to demonstrate the use of the presented framework, including (1) NFT expressed as a stateful contract (the evolution of the quantity of a given NFT token on the ledger is controlled by a minting policy, and should never exceed 1), and (2) message-passing as a stateful contract for asynchronous communication, whose state is the set of sent messages encoded as special tokens. Finally, our examples culminate in the application of this approach to multiple implementations of a bare-bones specification of an account simulation, which can be a starting point for designing and proving correct a custom account system within a larger contract, such as Marlowe. 

## Motivation: why is this CIP necessary?

Smart contracts on Cardano are stateless boolean predicates. The point of smart contract scripts, however, is to control the updates made by transactions to certain data on the ledger state. For example, a minting policy regulates updating the total supply of tokens under this policy, and a validator (output-locking) script regulates the removal of that UTxO entry from the UTxO set.

For this reason, every contract implementation ultimately consists of (possibly multiple interacting) scripts that ensure that the application of a transaction updates the ledger data according to the contract specification. This unifying requirement of controlling some underlying state suggests the need for a stateful approach to the specification of all contracts. A stateful specification of the evolution of contract data in response to transaction application allows us to meaningfully reason about whether a specification is correctly implemented. In particular, we require a specific relation between the evolution of the contract state and the underlying ledger state to be ensured by the implementing scripts of the contract. This can be summed up by "no transaction, adversarial or otherwise, is able to cause unwanted changes to assets or data controlled by the contract". All changes that are not possible according to the contract specification are "unwanted".

This CIP presents a framework, called the **structured contract formalism**, which is a minimal formalization the above. We propose an **add-on to the Agda ledger model** that enables developers to write contract specifications using the same small-step operational semantics used for the specification of the ledger itself, and implement them using Agda functions as scripts. To complete a structured contract instantiation, a specific proof obligation must be fulfilled to guarantee the integrity of the implementation. Note that no alterations of the existing model are required for building the structured contract framework, only additions.

Once instantiated (including a fulfilled proof obligation), contract authors may focus on reasoning about trace-based safety properties of the contract specification, since any implementation is guaranteed to uphold all proved safety properties. Trace-based properties are the standard for (stateful) program behaviour analysis, so using a framework in which contracts are expressed as (stateful) transition systems allows users to take advantage of it. This is because safety properties of correctly implemented small-step program specifications are guaranteed to hold at the ledger level.

Instantiating such a framework to build stateful contracts with verifiably correct implementations is most useful in the cases where the same code is used for running multiple instances of the same application with different parameters. This includes contracts such as Marlowe, Hydra, and various DEX implementations. The account simulation example showcases how the structured contract framework enables the EUTxO model to reap some of the benefits of account-based ledgers without giving up the advantages of the UTxO ledger. In addition, the multiple proposed implementations of the account simulation showcase how this stateful computation model can be used to compare correct implementations of a single specification.

For existing contracts, we argue that it is beneficial to build a small-step specification. The SC framework can then be instantiated with the specification and implementation of the existing contract, and, if the implementation is indeed correct, a proof can be constructed of its integrity. This, again, allows for reasoning about trace-based safety properties of the contract behaviour, which are guaranteed to hold on-chain.

## Specification

We specify the components of the structured contract framework that must be defined to instantiate it. This includes the functions that compute the contract state from the ledger state, and the contract input from the transaction. Additionally, we specify the simulation relation between the ledger specification and the contract specification which must be proved for the specified functions. We then give the specifications of the examples we intend to implement.

### Structured Contract Formalism

### Correctness of implementation

### Properties

A property is a predicate on an infinite sequence of states, i.e. `P : S x S -> Bool`, where `S` can be either the ledger state, or the contract state. A safety property [liveness]() is such that given any sequence of states, if this property is violated, it cannot be remedied later on in the sequence. This includes any property that is a quantification over all states in the sequence, or over all transitions between pairs of states in the sequence. Informally, a safety property states that "nothing bad ever happened during the entire (infinite) execution trace".

In [utxoprop](), for each structured contract `STRUC`, we define a function `pi_STRUC`, which maps valid execution traces of the ledger to valid traces of the contract specification. It is defined in terms of that contract's state projection function. As demonstrated in [utxoprop](), given a valid contract execution trace and a safety property `P` that it satisfies, the property `P . pi` is satisfied by any pre-image ledger trace.

## Examples

## Path to Active

### Acceptance Criteria

- [x] There is a variety of tools and services utilizing this standard:
  - [x] Trezor, Ledger
  - [x] cardano-cli
  - [x] cardano-wallet
  - [x] Blockfrost
  - [x] cardanoscan, cexplorer
  - ... and more

### Implementation Plan

- Available JavaScript library: [cip5-js](https://www.npmjs.com/package/@dcspark/cip5-js)

## Copyright

This CIP is licensed under [Apache-2.0](https://www.apache.org/licenses/LICENSE-2.0).

[CIP-0014]: https://github.com/cardano-foundation/CIPs/blob/645243e30b5aae109a70ec2b47af70dcc808bc56/CIP-0014

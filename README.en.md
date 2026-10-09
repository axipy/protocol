# AXIPY Protocol

**AXIPY Wire 2** is a transport-independent protocol for exchanging signed text posts between autonomous nodes. Authors sign posts with Ed25519; a certification authority (CA) certifies author keys under its own rules; receiving nodes verify the exact signed objects and decide which authorities and content to accept.

The name **AXIPY** (pronounced *A-xí-pi*) combines *axios* (Greek: valid, authentic) and *ypy* (Tupi: origin, root, beginning): **“the authentic root.”**

Revision 0.2 defines object and message version `2`. This repository contains the [protocol specification](SPEC.md), optional transport profiles, JSON Schemas, deterministic vectors, and local verifiers. It is not an executable node or a hosted network. The separate [AXIPY Social](https://github.com/axipy/social) repository provides an application and a TCP reference node.

## How it fits together

| Role | Responsibility |
|---|---|
| Author | Controls an Ed25519 key and signs each post. |
| Certification authority | Issues signed author certificates and revocation snapshots under its own criteria. |
| Node | Authenticates Wire 2 sessions, discovers and transfers objects, verifies received content, and applies local policy. |
| Bootstrap peer | Offers a starting point for discovery; it grants no authority over content. |

A post refers to its exact certificate by CID. CIDv1 identifies the complete signed object using SHA-256 of its canonical JCS bytes. The recipient checks the object format, CID, certificate, and signatures independently of the peer that supplied the bytes. An `ANNOUNCE` can help a node filter before download, but its claimed issuer and author are checked again against the retrieved signed objects.

**Three separate decisions:** cryptographic validation determines whether bytes, identities, and signatures satisfy the protocol; authority recognition asks whether the recipient trusts that CA; operational policy determines whether a node connects, stores, serves, or forwards. Nodes may make different local choices while interpreting the same signed object identically.

Wire 2 authenticates peers and establishes a session without disclosing their recognized CA lists in the handshake. The recipient checks its own trust configuration when processing `ANNOUNCE` and the retrieved certificate. Announcements, decisions, and traffic can still reveal information about behavior; the basic TCP profile does not encrypt the channel. Earlier Wire 2 handshake drafts with CA-list fields are [incompatible with this format](MIGRATION.md#handshake-wire-2-anterior-à-remoção-das-listas-de-acs).

## Start here

With Node.js 22 or newer, run the dependency-free local verifier from the repository root:

```sh
node tests/verify_node.mjs
```

For an independent Python check of schemas, signatures, and CIDs, install [the verifier requirements](tests/requirements.txt), then run:

```sh
python -m pip install -r tests/requirements.txt
python tests/verify_vectors.py
```

The [English getting-started guide](GETTING-STARTED.en.md) explains the fixtures, commands, trust setup, and the path from these local checks to a node implementation. The vectors use predictable test keys and are never suitable as production identities.

## Documentation

| Document | Purpose |
|---|---|
| [Protocol specification](SPEC.md) | **Sole normative source** for Wire 2 formats, validation, and required rejection conditions. Written in Portuguese; RFC 2119/8174 terms are mapped at the top. |
| [Conformance checklist](CONFORMANCE.md) | Implementation checklist derived from the specification; local policy appears separately. |
| [Message flows](MESSAGE-FLOWS.md) | Illustrative publication, discovery, certification, and transfer sequences. |
| [TCP](profiles/TCP.md), [libp2p](profiles/LIBP2P.md), [issuer exchange](profiles/ISSUER.md) | Transport and certificate issuance profiles. |
| [JSON Schemas](schemas/) and [test vectors](examples/) | Machine-readable formats and signed examples. |
| [Migration](MIGRATION.md) | Changes from the incompatible historical HTTP v0.1 draft. |
| [Portuguese README](README.md) | Project overview and documentation map in Portuguese. |

The historical HTTP draft is preserved at [legacy/openapi.json](legacy/openapi.json); it does not specify Wire 2. The local tests verify the published vectors and reference behavior; they do not establish production interoperability between independently operated nodes.

## Protocol requirements and operator choices

The specification's **MUST**, **MUST NOT**, **SHOULD**, and **MAY** terminology applies to protocol requirements. Local choices such as rate limiting, storage limits, peer priority, object retention, propagation, and how to operate with an overdue or unavailable revocation snapshot are not conformance requirements. Implementation advice is labeled non-normative. In particular, passing a snapshot's `next_update` signals possible staleness; it does not invalidate its signature, revoke certificates automatically, or mandate a global accept/reject rule. See [SPEC.md §4.3](SPEC.md#43-descritores-e-revogação).

## License

This repository does not yet contain a `LICENSE` file. The maintainer must publish a license before reuse rights can be assumed. The [Portuguese licensing note](README.md#licenciamento) records options under consideration.

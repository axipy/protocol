# Getting started with AXIPY Wire 2

This guide takes you from the checked-in vectors to an implementation. The [specification](SPEC.md) is the sole normative source. Commands here exercise local fixtures; this repository does not start a CA, a peer network, or a DHT.

## 1. Verify the reference material

Install Node.js 22 or newer. From the root of a checkout of `axipy/protocol`:

```sh
node tests/verify_node.mjs
```

The Node verifier uses the built-in runtime and reads the checked-in examples. It checks the published schema and cryptographic fixtures, including negative cases; it does not need `npm install`.

For a second implementation of the checks, install Python 3.11 or newer and the listed dependencies:

```sh
python -m pip install -r tests/requirements.txt
python tests/verify_vectors.py
```

On Windows, invoke `py -3` in place of `python` if that is how Python is installed. The checks should exit successfully; inspect any failure before relying on a changed schema or vector.

The fixtures were generated from predictable seeds. To reproduce them, run:

```sh
node tests/generate_vectors.mjs
```

This command rewrites files under `examples/`. Compare your checkout afterward, for example with `git diff -- examples`, to see whether generation changed the committed vectors. Never use the example keys for a live identity or CA.

## 2. Read and validate an object

Start with [the signed examples](examples/README.md), [the JSON Schemas](schemas/), and [the reference codec](reference/codec.mjs). For any post received from a peer, the validation sequence in [SPEC.md §5.1](SPEC.md#51-ordem-normativa-de-validação-de-um-post) governs parsing, schema, CID, the exact referenced certificate, key linkage, and signatures. A post's `created_at` is a signed author declaration, not proof of a real-world timestamp. The text bytes are not Unicode-normalized before signing or CID construction.

An `ANNOUNCE` is an early claim. Its issuer and author fields cannot stand in for validation of the retrieved post and certificate. The same object may be fetched from another peer; the sender does not establish its authority.

## 3. Supply local trust and decide operations

Configure the CA public keys your node recognizes through your own implementation or operator process. A key offered by a discovered `authority-descriptor` is not automatically trusted. Verify the certificate with a locally recognized CA key, then keep that result distinct from the operator's decision to accept, store, or forward a valid post.

The Wire 2 handshake exchanges node keys, nonces, version support, and a session identifier. It does not exchange recognized CA lists. A recipient may decline an `ANNOUNCE` by its declared `issuer_id` before downloading the object; if it proceeds, it checks the actual issuer and signatures after retrieval. Its response need not reveal its accepted authorities.

Revocation snapshots are signed and addressed by CID. Their `next_update` field declares when the issuer expects to publish newer information. At or after that UTC timestamp, the snapshot may be stale; it remains the same authenticated object. Whether to pause acceptance, keep operating, or seek newer information is local policy. The protocol does not require a CA request for every post.

Rate limits, storage quotas, peer selection, retention, and propagation are local policies, not protocol conformance tests. The [conformance checklist](CONFORMANCE.md) keeps those decisions separate from mandatory validation and labels implementation advice as non-normative.

## 4. Connect peers when implementing Wire 2

Use the [Wire 2 message definitions](SPEC.md#7-protocolo-de-comunicação-abstrato-axipy-wire-2) and [illustrative flows](MESSAGE-FLOWS.md) to implement session establishment, announcement, object retrieval, and provider discovery. Choose an advertised transport profile: [direct TCP](profiles/TCP.md) or [libp2p](profiles/LIBP2P.md). [Issuer exchange](profiles/ISSUER.md) handles certificate requests separately from peer publication traffic.

For an executable application and a TCP reference node, see [AXIPY Social](https://github.com/axipy/social#readme). Its README documents its own startup, trusted-authority configuration, and integrated two-node test. That application is maintained in a separate repository; this protocol checkout only supplies specifications and local verifiers.

## 5. Consume validated objects in an application

An application can render a post after its node has checked the exact signed post and referenced certificate and applied its own authority and content policy. The post's `text` is plain text; the protocol does not define Markdown rendering, embedded media, a feed order, or a moderation UI. Keep the original signed bytes and CID available if the application needs to export or relay the object. Use the [post schema](schemas/post.schema.json) for field names and [SPEC.md §5](SPEC.md#5-posts-apenas-texto-puro) for the validation boundary. This repository offers no application SDK or API endpoint for this step.

## Compatibility and limits

Wire 2 object and message formats are incompatible with the historical HTTP v0.1 draft; follow [MIGRATION.md](MIGRATION.md) when integrating older material. Local verifier success does not prove interoperability of independently developed, separately operated nodes. Signatures authenticate protocol objects, while the basic TCP profile alone does not encrypt the traffic. See [security considerations](SPEC.md#11-considerações-de-segurança) for the exact boundary.

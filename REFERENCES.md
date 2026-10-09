# Padrões consolidados aproveitados

O protocolo AXIPY não reimplementa o IPFS inteiro; reaproveita formatos e princípios e especifica regras próprias de autoria/autoridades.

- IPFS, representação, roteamento e transferência de conteúdo: https://docs.ipfs.tech/concepts/how-ipfs-works/
- CIDv1: https://specs.ipfs.tech/cid/ (multicodec `raw`, multihash SHA-256, multibase base32).
- Endereçamento por conteúdo: https://docs.ipfs.tech/concepts/content-addressing/
- Bitswap como referência conceitual para pedido de blocos por CID: https://docs.ipfs.tech/concepts/bitswap/ — **AXIPY Wire NÃO é compatível com Bitswap**.
- libp2p, definição de protocolos próprios de aplicação e streams: https://libp2p.io/docs/protocols/
- libp2p multiaddr e PeerId: https://libp2p.io/docs/addressing/ e https://libp2p.io/docs/peers/
- Noise Protocol Framework, como comparação para autenticação de canal, troca de chaves e sigilo: https://noiseprotocol.org/noise.html — o handshake AXIPY não implementa Noise.
- Nostr NIP-01, eventos assinados e relays: https://github.com/nostr-protocol/nips/blob/master/01.md
- AT Protocol, visão geral de identidade, repositórios e serviços: https://atproto.com/guides/overview
- Secure Scuttlebutt, feeds assinados e replicação: https://ssbc.github.io/scuttlebutt-protocol-guide/
- RFC 8785 JSON Canonicalization Scheme: https://www.rfc-editor.org/rfc/rfc8785
- RFC 8032 EdDSA: https://www.rfc-editor.org/rfc/rfc8032
- RFC 7517 JWK: https://www.rfc-editor.org/rfc/rfc7517
- RFC 7638 JWK thumbprints: https://www.rfc-editor.org/rfc/rfc7638
- JSON Schema 2020-12: https://json-schema.org/draft/2020-12

**Distinção fundamental:** IPFS não impõe identidade certificada ou política de autoridades por padrão; AXIPY adiciona essa semântica por cima de objetos identificados por CID, sem obrigar o uso da rede IPFS para publicação ou recuperação.

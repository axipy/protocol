# AXIPY Protocol — revisão 0.2

**Protocolo peer-to-peer para distribuição de objetos sociais textuais assinados.** Não é uma rede social pronta, um conjunto de endpoints HTTP, um banco de dados, uma implementação obrigatória em Node.js, nem uma cópia do IPFS.

## O que a AXIPY determina

- **Conteúdo:** somente texto puro em posts; metadados e certificados são JSON estruturados.
- **Autoria:** Ed25519; o usuário assina cada post.
- **Identidade certificada:** uma autoridade certificadora assina um certificado para a chave pública do usuário. Autoridade e nó são papéis distintos.
- **Confiança:** cada nó escolhe quais autoridades reconhece e quais peers/objetos aceita.
- **Endereçamento:** CIDv1/IPFS `raw` + `sha2-256` calculado dos bytes JSON canonicalizados do objeto **assinado completo**.
- **Comunicação:** mensagens AXIPY `HELLO/CHALLENGE/CONFIRM/READY`, `ANNOUNCE/DECISION`, `WANT/BLOCK`, `PEERS`, `FIND_PROVIDERS/PROVIDERS`, `ERROR` etc. Não são verbos nem rotas HTTP.
- **Descoberta:** bootstrap e indicação de peers/provedores de conteúdo. Um bootstrap não dá autorização especial a ninguém.
- **Transportes:** TCP puro e libp2p são dois perfis opcionais **especificados** nesta revisão; HTTP, se existir, é gateway/integração opcional, não requisito nuclear.

## Diagrama da troca entre nós

```text
                    AC (certificadora)
                    assina certificado
                             |
                             v
autor -- assina --> post JSON + referência ao certificado
                             |
                         CIDv1(raw)
                             |
                    [Nó A possui bloco]
                             |
          descobrir Nó B via bootstrap / peers
                             |
         HELLO -> CHALLENGE -> CONFIRM -> READY
                             |
         ANNOUNCE(CID, autoridade, autor)
                             |
                    B decide localmente
          / aceita pré-filtro      \ recusa
         v                         X
       WANT(CID)
         |
       BLOCK(post)
         |
   WANT(certificate CID) se necessário
         |
       BLOCK(certificate)
         |
   B confere CID + assinatura AC + assinatura autor
         |
    aceita ou recusa segundo política própria
```

## Arquivos deste pacote

| Caminho | Conteúdo |
|---|---|
| `SPEC.md` | Especificação normativa central: objetos, assinaturas, confiança, handshake, mensagens, CID, validação e limites |
| `profiles/TCP.md` | Framing de mensagens sobre TCP direto |
| `profiles/LIBP2P.md` | Stream libp2p `/axipy/2.0.0`, vínculo com PeerId, descoberta opcional |
| `profiles/ISSUER.md` | Emissão de certificados como serviço distinto de nós |
| `schemas/` | **30 schemas** JSON Schema Draft 2020-12, incluindo 14 corpos de mensagens |
| `examples/` | Vetores Ed25519 reais com chaves **de teste**, CIDs, anúncios, handshake, posts, resposta e cadastro |
| `reference/codec.mjs` | Código de referência para JCS, Ed25519, CID, enquadramento TCP; **não é servidor** |
| `tests/generate_vectors.mjs` | Reproduz deterministicamente exemplos assinados (somente testes) |
| `tests/verify_node.mjs` | Verificação independente em Node.js |
| `tests/verify_vectors.py` | Schemas e verificação cruzada Ed25519/CID em Python |
| `MESSAGE-FLOWS.md` | Cenários passo a passo: três bootstraps, cadastro, propagação, localização por CID e divisões de confiança |
| `CONFORMANCE.md` | Checklist verificável para implementações em linguagens independentes |
| `DECISIONS.md` | Decisões fixadas e fronteiras do protocolo |
| `MIGRATION.md` | Diferenças para o rascunho anterior v0.1 |
| `REFERENCES.md` | Fontes técnicas adotadas e distinções em relação a IPFS/libp2p |

## Verificações reproduzíveis

Node.js 22+ e Python 3.11+ com `cryptography` e `jsonschema`:

```bash
node tests/generate_vectors.mjs
node tests/verify_node.mjs
python -m pip install -r tests/requirements.txt
python tests/verify_vectors.py
```

Os arquivos `examples/` são determinísticos e contêm chaves e assinaturas geradas de seeds previsíveis, **jamais para uso real**. Nenhuma biblioteca npm é necessária para verificar os vetores em Node. Os testes Python usam uma codificação compatível com o conjunto restrito dos vetores; implementações gerais devem usar JCS RFC 8785 completo (não `json.dumps(sort_keys=True)` indiscriminadamente).

## Estado real

A revisão 0.2 entrega **uma especificação concreta**, seus schemas, perfis e vetores interoperáveis. **Não** entrega uma rede libp2p executável, serviço de certificadora online, descoberta DHT implantada ou interoperabilidade de produção já demonstrada entre hosts distantes. Uma implementação conforme estes arquivos poderá escolher os perfis de transporte previstos sem alterar a validade dos objetos.

Esta versão é uma **proposta para ajuste**, não um padrão formalmente ratificado. As decisões anteriores sobre certificados, autonomia de nós e texto puro foram preservadas; a substituição de endereços/rotas HTTP por mensagens AXIPY e de IDs proprietários por CIDv1 é intencional.

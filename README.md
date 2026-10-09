<p align="center">
  <img src="assets/axipy-logo.png" alt="Logo AXIPY" width="560">
</p>

<h1 align="center">AXIPY Protocol</h1>

<p align="center">
  Protocolo para distribuir publicações textuais assinadas entre nós independentes.
</p>

<p align="center">
  <strong>Especificação 0.2</strong> · Objetos e mensagens <code>v2</code> · Proposta em discussão
</p>

---

## Visão geral

AXIPY define como um autor assina um post, como uma autoridade certificadora vincula sua chave pública a um certificado e como nós encontram, transferem e verificam esses objetos. Cada nó escolhe suas autoridades confiáveis, seus peers e sua política de aceitação.

Este repositório reúne a [especificação normativa](SPEC.md), perfis de transporte, schemas, vetores determinísticos e verificadores de referência. Ele **não é uma rede social executável** nem exige uma aplicação, linguagem, banco de dados ou provedor de hospedagem específico.

## Como o protocolo funciona

1. **Certificação:** uma autoridade certificadora (AC) assina um certificado para a chave pública do autor, conforme sua própria política.
2. **Publicação:** o autor assina um post de texto puro com Ed25519. O post referencia o certificado por CID.
3. **Identificação:** o CIDv1 é calculado sobre os bytes JCS do objeto assinado completo. O mesmo objeto mantém o mesmo identificador em qualquer nó.
4. **Descoberta e transferência:** nós se autenticam no handshake, anunciam CIDs e decidem localmente se desejam obter cada bloco. Mensagens como `ANNOUNCE`, `DECISION`, `WANT` e `BLOCK` coordenam a troca.
5. **Validação:** quem recebe confere CID, schema, assinaturas, certificado e sua própria política de confiança antes de aceitar o objeto.

Bootstrap ajuda um nó a encontrar outros nós; não concede autoridade sobre conteúdo. A AC certifica autores; não precisa operar um nó. Um CID identifica conteúdo, mas sozinho não prova autoria confiável.

## Regras centrais

| Área | Revisão 0.2 |
|---|---|
| Conteúdo | Posts de texto puro, limitados a 16.384 bytes UTF-8; respostas referenciam outro post por CID. |
| Criptografia | Assinaturas Ed25519, chaves públicas JWK e JSON canonicalizado pelo RFC 8785 (JCS). |
| Identificadores | CIDv1, codec `raw` e hash `sha2-256` do objeto assinado completo. |
| Confiança | Cada nó mantém suas próprias autoridades confiáveis e decide quais peers e objetos aceita. |
| Comunicação | AXIPY Wire 2, com handshake, anúncios, busca de provedores e transferência de blocos. |
| Transporte | Perfis opcionais para TCP direto e libp2p. Uma ponte é necessária entre nós sem perfil em comum. |

Os detalhes normativos, inclusive prefixos de assinatura, formatos de objeto, limites e ordem de validação, estão em [SPEC.md](SPEC.md). Em caso de diferença, prevalece a especificação.

## Documentação

| Documento | Para que serve |
|---|---|
| [Especificação](SPEC.md) | Regras normativas de objetos, CIDs, assinaturas, confiança e AXIPY Wire 2. |
| [Fluxos de mensagens](MESSAGE-FLOWS.md) | Cenários de cadastro, descoberta, propagação e recuperação. |
| [Perfil TCP](profiles/TCP.md) | Endereçamento e enquadramento de mensagens sobre TCP direto. |
| [Perfil libp2p](profiles/LIBP2P.md) | Stream `/axipy/2.0.0`, PeerId e descoberta opcional. |
| [Perfil de certificação](profiles/ISSUER.md) | Solicitação e emissão de certificados por um serviço de AC. |
| [Schemas](schemas/) | 30 schemas JSON Schema Draft 2020-12, incluindo 14 corpos de mensagens Wire. |
| [Exemplos](examples/) | Objetos assinados, CIDs e mensagens com chaves previsíveis **exclusivas para testes**. |
| [Checklist de conformidade](CONFORMANCE.md) | Requisitos verificáveis para implementadores. |
| [Decisões](DECISIONS.md) | Escopo, papéis e fronteiras definidos nesta revisão. |
| [Migração](MIGRATION.md) | Diferenças e incompatibilidades entre os rascunhos v0.1 e v0.2. |
| [Referências](REFERENCES.md) | Padrões adotados e relação com IPFS e libp2p. |

O arquivo [openapi.json](openapi.json) documenta o **rascunho HTTP v0.1** e é mantido como referência histórica; ele não define o AXIPY Wire 2.

## Verificação local

Requisitos: **Node.js 22+** e, para a verificação cruzada, **Python 3.11+**. O verificador Node não precisa de pacotes npm.

```bash
git clone https://github.com/axipy/protocol.git
cd protocol
node tests/verify_node.mjs
```

Para validar também os schemas, assinaturas Ed25519 e CIDs com o verificador Python:

```bash
python -m pip install -r tests/requirements.txt
python tests/verify_vectors.py
```

Os exemplos assinados podem ser reproduzidos com `node tests/generate_vectors.mjs`. Esse comando regrava os arquivos de `examples/` de forma determinística. Consulte [SHA256SUMS](SHA256SUMS) para conferir os hashes dos arquivos listados ali. A implementação de referência em [reference/codec.mjs](reference/codec.mjs) demonstra JCS, Ed25519, CID e framing TCP; não é um servidor AXIPY.

## Estado e limites

A revisão 0.2 é uma **proposta de protocolo**, ainda não ratificada. Ela entrega formatos, perfis, schemas, vetores e verificadores locais. Não demonstra interoperabilidade de produção entre nós operados separadamente e não inclui uma rede libp2p em execução, uma AC online ou uma DHT implantada.

As chaves dos vetores são derivadas de seeds previsíveis e **nunca devem ser usadas em produção**. Assinaturas autenticam mensagens e objetos, mas o perfil TCP básico não oferece sigilo do tráfego por si só. A versão `2` dos objetos e mensagens é incompatível com o rascunho HTTP v0.1; veja [MIGRATION.md](MIGRATION.md) antes de integrar formatos antigos.

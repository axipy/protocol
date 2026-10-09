# Mudanças de AXIPY v0.1 para v0.2

Esta revisão é a **especificação AXIPY em desenvolvimento** para objetos e mensagens v2, não um patch automático do backend original nem migração de objetos já publicados. O [OpenAPI HTTP v0.1](legacy/openapi.json) permanece como registro histórico, com as limitações de schema descritas em [legacy/README.md](legacy/README.md).

| Eixo | Rascunho HTTP v0.1 | AXIPY Wire 2 (revisão 0.2) |
|---|---|---|
| Identificador de posts | `axp1_` de SHA-256 do payload | CIDv1 `raw` de JCS do objeto assinado completo |
| Identificador do certificado | `axc1_` do payload | CIDv1 de JCS do certificado assinado completo |
| Referência `reply_to` | ID `axp1_...` | CIDv1 do post pai |
| Referência `certificate_id` | `axc1_...` | `certificate_cid` |
| Transporte base | rotas HTTP normativas | mensagens abstratas AXIPY Wire 2, perfis TCP e libp2p opcionais |
| Descoberta | URLs bootstrap e endpoint HTTP | descriptors assinados, multiaddrs, PEERS e consulta de providers |
| Envio | anúncio HTTP e download HTTP | ANNOUNCE/DECISION + WANT/BLOCK por CID |
| Certificadora | endpoint HTTP no mesmo perfil | serviço independente Issuer Exchange 2, possível gateway HTTP separado |
| Assinaturas | contextos `/1` | contextos `/2`, nunca intercambiáveis |

**Não reutilizar** post JSON antigo mudando apenas a versão, pois a assinatura e as referências deixariam de corresponder às regras. Não transformar automaticamente certificado antigo em certificado v2. Novos objetos precisam ser emitidos e assinados conforme esta revisão.

A integridade do código de uma instalação (Genesis) é distinta da validação de objetos AXIPY. Ela pode continuar existindo em implementações específicas, mas **não é condição universal de conformidade do protocolo**.

## Handshake Wire 2 anterior à remoção das listas de ACs

Rascunhos Wire 2 anteriores incluíam `HELLO.authorities`, `CHALLENGE.accepted_authorities` e `READY.accepted_authorities`. Esses campos foram removidos para que a sessão não divulgue diretamente as ACs reconhecidas por cada nó. Os schemas de corpo continuam fechados: um peer atualizado rejeita mensagens com os campos antigos, e um peer antigo que os exige rejeita o novo handshake. Portanto, os dois formatos **não interoperam na mesma sessão**; ambos os lados precisam usar os schemas de handshake atuais. A versão principal de objetos/mensagens continua `2` nesta especificação em desenvolvimento, mas isso não promete compatibilidade com todo rascunho Wire 2 anterior.

A mudança não altera posts, certificados, CIDs, prefixos de assinatura nem o `issuer_id` de `ANNOUNCE`. O receptor passa a consultar seu conjunto local de ACs ao decidir sobre anúncios e ao validar o certificado obtido, independentemente da autenticação do peer na sessão.

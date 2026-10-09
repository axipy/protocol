# Mudanças de AXIPY v0.1 para v0.2

Esta revisão é um **novo rascunho do protocolo**, não um patch automático do backend original nem migração de objetos já publicados.

| Eixo | Rascunho v0.1 | Rascunho v0.2 |
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

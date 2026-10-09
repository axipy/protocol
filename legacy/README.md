# Histórico HTTP v0.1

[`openapi.json`](openapi.json) registra as rotas do rascunho HTTP v0.1. AXIPY Wire 2 não usa essas rotas como transporte ou contrato normativo; consulte a [especificação atual](../SPEC.md) e os perfis [TCP](../profiles/TCP.md) e [libp2p](../profiles/LIBP2P.md).

Os caminhos `$ref` foram ajustados após a mudança de diretório para continuarem resolvendo arquivos existentes em `../schemas/`. **Esses schemas descrevem objetos v2**, não uma reconstrução fiel dos formatos HTTP v0.1. Assim, o OpenAPI histórico serve para consultar o desenho das rotas, e não para gerar ou validar clientes v0.1. Nenhum schema v0.1 foi recriado nesta revisão. Para diferenças de formatos e identificadores, veja [MIGRATION.md](../MIGRATION.md).

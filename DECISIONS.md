# AXIPY v0.2 — decisões normativas e fronteiras

## Fixado nesta revisão

1. O **protocolo não define aplicativos** nem torna implementação oficial obrigatória.
2. **Autoridade certificadora, nó, usuário e bootstrap** são papéis separados. Certificadora pode existir sem ser nó e nó sem ser certificadora.
3. A AXIPY pode operar uma AC inicial e três nós bootstrap sem criar privilégio protocolar.
4. O cadastro do usuário é decidido pela AC; o certificado vincula a chave pública do usuário e é assinado pela AC.
5. O usuário **assina cada post**, não a AC. Um post pode ser gerado fora da aplicação oficial, inclusive manualmente.
6. O conteúdo da publicação é **texto puro**, limitado a 16 KiB UTF-8. Sem upload de imagens, vídeo ou áudio no protocolo.
7. **Cada nó define suas autoridades confiáveis**, seus peers, política de aceitação, armazenamento, exposição e propagação.
8. CIDs seguem o formato **CIDv1 raw SHA-256**, calculados de JCS do objeto assinado. Um nó confere conteúdo + assinatura + certificado, não só CID.
9. **Descoberta, transmissão e validação** são responsabilidades distintas. Bootstrap é convite para encontrar peers, não ponto central obrigatório.
10. O núcleo define **mensagens AXIPY**, não endpoints HTTP. TCP direto e libp2p são perfis opcionais. Uma implementação anuncia o(s) perfil(is) efetivamente suportado(s).
11. Versões v0.1 e v0.2 têm formatos incompatíveis nos IDs e wire. Uma ponte, se existir, deve traduzir explicitamente e **não** reaproveitar assinaturas indevidamente.
12. Registro do nó que encaminhou a certificação pode ser observado e atestado pela AC; campos de origem admitem `null` quando não atestados.

## Deliberadamente fora do núcleo

- Feeds, timelines, seguidores, busca pública, moderação e ranking.
- Forma de persistência, diretórios, backend, dependências, interface, login ou cadastro visual.
- Transportes obrigatórios para **todos** os nós; uma ponte é necessária entre perfis incompatíveis.
- DHT global, GossipSub, replicação compulsória ou garantia de disponibilidade.
- Certificação de identidade civil obrigatória, centralização de ACs ou confiança universal na AC AXIPY.
- Auditoria do código do servidor ou comprovação da integridade do processo remoto.
- Uma raiz Genesis obrigatória para aceitar todos os usuários; cada nó mantém sua própria lista de ACs confiáveis.

## Compatibilidade com a ideia original

A capacidade de um nó de negar comunicação ou objetos por autoridade permanece. Um nó pode consumir AXIPY e Autoridade B, recusando D. A federação pode formar subconjuntos de confiança dentro do mesmo protocolo, ligados por pontes voluntárias. Os CIDs são identificadores do conteúdo, não de uma instância que o hospedou.

# Magic — avaliador de cartas de Magic: The Gathering

App web (uma página, sem build) para avaliar o **preço de mercado** de cartas de MTG e manter a **coleção** do usuário.

## O que faz
- Busca carta por nome (inglês ou português), escolhe edição e acabamento (normal/foil/etched).
- Mostra preço TCGplayer (USD) e Cardmarket (EUR), convertidos para R$ pelo câmbio comercial (AwesomeAPI; reserva: BCE/Frankfurter).
- **Toda carta avaliada entra na coleção** do usuário, a menos que a opção "Carta da coleção" esteja desmarcada.
- Avalia listas coladas (formato de exportação de decks) e soma à coleção.
- Aba "Minha coleção": valor total, ajuste de quantidade, atualização de preços, exportação CSV.
- Grava uma fotografia diária de preço por impressão consultada (`price_snapshots`) — base para histórico.

## Stack
- Front: `index.html` (HTML + JS puro, supabase-js via CDN), publicado no GitHub Pages.
- Dados de cartas: [Scryfall API](https://scryfall.com/docs/api).
- Banco e login: Supabase (projeto "Magic Valuation"), e-mail + senha, regras RLS por usuário.

## Chaves
O `index.html` usa só a **chave publicável** do Supabase, que pode ficar no código: a proteção dos dados vem das políticas RLS. Nunca coloque a `service_role`/secret key no front.

## Banco (resumo)
- `collection_items`: coleção por usuário (`user_id`, `scryfall_id`, `finish`, `quantity`, preços).
- `price_snapshots`: preço por impressão por dia.
- `add_to_collection(item jsonb, qty int, only_if_new bool)`: insere ou soma quantidade.

## Próximos passos
- Histórico de preços e gráfico por carta.
- Preço Brasil (LigaMagic) — depende de parceria/API.
- Força de jogo (17lands, EDHREC).

Magic: The Gathering © Wizards of the Coast. Projeto de fã, sem vínculo com a Wizards.

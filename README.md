# Magic — avaliador de cartas de Magic: The Gathering

App web (uma página, sem build) para avaliar o **preço de mercado** de cartas de MTG e manter a **coleção** do usuário.

## O que faz
- Busca carta por nome (inglês ou português), escolhe edição e acabamento (normal/foil/etched).
- Mostra preço TCGplayer (USD) e Cardmarket (EUR), convertidos para R$ pelo câmbio comercial (AwesomeAPI; reserva: BCE/Frankfurter).
- **Toda carta avaliada entra na coleção** do usuário, a menos que a opção "Carta da coleção" esteja desmarcada.
- Guarda o nome digitado (ex.: em português) além do nome em inglês, editável na coleção.
- Guarda custo de mana, cor (Branco, Azul, Preto, Vermelho, Verde, Multicolorida, Incolor) e tipo da carta; coluna e filtro de cor na coleção.
- Edições em inglês e em português na grade (etiqueta PT, cor da borda); impressões em português sem preço próprio usam o preço da mesma carta em inglês (marcado com *).
- Idioma da carta informado pelo usuário (Inglês, Português…), editável na carta e na coleção; coluna e filtro de idioma. A atualização diária não altera o idioma.
- Tela de detalhes: clique numa carta (Minha coleção ou Lista) para ver imagem ampliada (com zoom e verso), texto, preços, histórico, dados da impressão, força de jogo e editar quantidade, idioma e nome em português.
- Ano da sua carta (ex.: ano impresso), escolhido ao adicionar e editável na coleção e nos detalhes; por padrão, o ano da edição.
- Busca por nome (inglês ou português) e filtro por coleção/edição em Minha coleção.
- Guarda a raridade (Comum, Incomum, Rara, Mítica…) e se a carta está na Reserved List; filtro por raridade na coleção.
- Avalia listas coladas (formato de exportação de decks) e soma à coleção.
- Aba "Minha coleção": valor total, ajuste de quantidade, atualização de preços, exportação CSV.
- Quando a carta tem várias edições, mostra todas: clicar na imagem só mostra a edição (prévia); "+ Adicionar" guarda (várias edições podem ser adicionadas); 🔍 abre os detalhes. Carta com edição única entra automaticamente. Nenhum clique apaga nada.
- Força de jogo: ranking de popularidade no Commander (EDHREC), Penny Dreadful e legalidade por formato.
- Histórico de preço por edição e do valor total da coleção (gráficos).
- Atualização diária automática: o pg_cron chama a Edge Function `refresh-prices` às 07:17 (Brasília), que reavalia todas as cartas de todas as coleções e grava `price_snapshots` e `collection_value_history`.

## Fontes de preço
- **Scryfall** (TCGplayer US$ e Cardmarket €): versão em inglês; para impressões em outro idioma sem preço próprio, usa a mesma carta em inglês.
- **CardTrader** (API oficial): preço de anúncios reais **no idioma da sua carta** (ex.: português), inclusive edições só estrangeiras como *Fourth Edition Black Bordered* (1995–96). Edge Function `cardtrader-sync`, diária às 07:47 (Brasília) e no botão "Atualizar preços". Requer o secret `CARDTRADER_TOKEN` nas Edge Functions do Supabase.
- **Melhor disponível** (padrão em Minha coleção): CardTrader no idioma → CardTrader em outro idioma → TCGplayer → Cardmarket. A fonte aparece ao lado do preço (CT, CT*, TCG, TCG·EN, MKM).
- **Brasil**: LigaMagic e MYP Cards não têm API pública; o app mostra o link da LigaMagic.

## Stack
- Front: `index.html` (HTML + JS puro, supabase-js via CDN), publicado no GitHub Pages.
- Dados de cartas: [Scryfall API](https://scryfall.com/docs/api).
- Banco e login: Supabase (projeto "Magic Valuation"), e-mail + senha, regras RLS por usuário.

## Chaves
O `index.html` usa só a **chave publicável** do Supabase, que pode ficar no código: a proteção dos dados vem das políticas RLS. Nunca coloque a `service_role`/secret key no front.

## Banco (resumo)
- `collection_items`: coleção por usuário (`user_id`, `scryfall_id`, `finish`, `quantity`, preços).
- `price_snapshots`: preço por impressão por dia.
- `collection_value_history`: valor total da coleção por usuário por dia.
- `add_to_collection(item jsonb, qty int, only_if_new bool)`: insere ou soma quantidade.
- `private.app_config`: token da rotina diária (fora da API). Edge Function `refresh-prices` (verify_jwt desligado; valida o token via `verify_cron_token`, executável só pelo service_role).

## Próximos passos
- Preço Brasil (LigaMagic) — depende de parceria/API.
- Força de jogo (17lands, EDHREC).

Magic: The Gathering © Wizards of the Coast. Projeto de fã, sem vínculo com a Wizards.

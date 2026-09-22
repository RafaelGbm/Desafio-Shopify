# EC Vendedores — Section via Metaobject

Section `ec-vendors-metaobject` que renderiza todas as entradas publicadas de um metaobject (padrão: `store_vendor`).

## Arquivos

| Arquivo | Função |
| --- | --- |
| `sections/ec-vendors-metaobject.liquid` | Section, lógica de busca e schema |
| `snippets/ec-vendor-card.liquid` | Card de um vendedor |
| `assets/ec-vendors-metaobject.css` | Estilos (carregados só quando a section renderiza) |

Copie as três pastas para a raiz do tema (testado como estrutura Dawn / OS 2.0).

## 1. Criar a definição do metaobject

Admin › **Configurações › Dados personalizados › Metaobjects › Adicionar definição**

- Nome: `Vendedor` — Tipo (handle): `store_vendor`
- Campos:

| Chave | Tipo | Obrigatório |
| --- | --- | --- |
| `name` | Texto de linha única | sim |
| `role` | Texto de linha única | não |
| `photo` | Arquivo (imagem) | não |
| `tier` | Texto de linha única, lista de valores: `gold`, `silver`, `bronze` | não |
| `products` | Produto (lista) | não |

- Em **Opções**, ative **Ativo/Rascunho** (publicável) e **Acesso à loja virtual**. Sem acesso à loja, o Liquid não enxerga o metaobject; com status publicável, só entradas **Ativas** aparecem.

## 2. Adicionar a section

Editor de temas › qualquer template › **Adicionar seção › EC Vendedores**.

Configurações disponíveis: título, subtítulo, rótulo dos produtos, tipo do metaobject, máximo de vendedores/produtos, itens por linha (desktop 1–6, mobile 1–2), espaço entre cards, cor da borda, cor de fundo do card, espaçamento interno, arredondamento, fundo e espaçamentos da seção.

Breakpoints: `< 750px` usa a coluna mobile; `750–989px` usa `min(desktop, 2)`; `≥ 990px` usa a coluna desktop.

## Comportamento sem dados

- **Loja**: nada é renderizado (nem o CSS é carregado).
- **Editor de temas**: aparece um aviso explicando que não há entradas publicadas.
- Campos ausentes (foto, cargo, medalha, produtos) são omitidos; foto sem valor cai num avatar placeholder, produto sem imagem cai no ícone de camiseta.
- Produtos excluídos/despublicados referenciados no vendedor são ignorados.
- Cores apagadas no editor viram `transparent` (ex.: borda apagada = card sem borda visível).

## Limites

- O Liquid retorna no máximo 50 entradas de um metaobject sem `paginate`. Para mais que isso, a section precisa de paginação.

## Validação já realizada

Feita em preview local (Liquid renderizado com liquidjs e metaobjects simulados), sem loja Shopify:

- `shopify theme check`: 0 problemas na section e no snippet.
- Renderização com 0, 6, 9 e 12 registros; com 0 registros a section não gera HTML nem carrega CSS na loja, e mostra aviso só no Theme Editor.
- Casos de borda: nome longo (truncado com `…` e `title`), vendedor sem foto, sem medalha, sem produtos, sem nome e com referência de produto excluída.
- Responsividade conferida em 1440px, 820px e 390px.
- Lighthouse (mobile), página vazia × página com 12 vendedores:

| Métrica | 0 vendedores | 12 vendedores |
| --- | --- | --- |
| Performance | 99 | 99 |
| CLS | 0 | 0 |
| TBT | 0 ms | 0 ms |
| Elementos no DOM | 2 | 503 |
| CSS da section | não carregado | 4 KB (sem gzip) |

## Checklist de validação

- [ ] Criar ≥ 5 entradas ativas com medalhas diferentes → conferir grid 3 colunas (desktop) e 1 coluna (mobile)
- [ ] Mudar colunas, cores e padding no editor → refletir em tempo real
- [ ] Deixar 1 entrada como Rascunho → não aparece
- [ ] Colocar todas como Rascunho (0 registros) → section some na loja; aviso aparece no editor
- [ ] Lighthouse mobile antes/depois na página com a section
- [ ] `shopify theme check` sem erros

## Preview

```bash
npm i -g @shopify/cli
shopify theme dev --store <sua-loja>.myshopify.com          # preview local com hot reload
shopify theme push --unpublished --store <sua-loja>.myshopify.com   # gera tema não publicado + link de preview
```

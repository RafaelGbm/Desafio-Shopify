# EC Vendedores — Section via Metaobject

Section customizada (`ec-vendors-metaobject`) que renderiza os vendedores cadastrados como metaobject no Admin. Testada em cima da estrutura do tema Dawn (OS 2.0), mas não depende de nada específico do Dawn além da estrutura padrão de pastas.

## Arquivos

- `sections/ec-vendors-metaobject.liquid` — a section, busca os metaobjects e monta o schema
- `snippets/ec-vendor-card.liquid` — o card de cada vendedor
- `assets/ec-vendors-metaobject.css` — estilo, só carrega quando a section renderiza algo

Copia as três pastas pra raiz do tema e já funciona.

## Criando o metaobject

Antes de usar a section precisa da definição em **Configurações › Dados personalizados › Metaobjects › Adicionar definição**.

- Nome: `Vendedor`
- Tipo (handle): `store_vendor`

Campos:

| campo | tipo | obrigatório |
| --- | --- | --- |
| `name` | texto de linha única | sim |
| `role` | texto de linha única | não |
| `photo` | arquivo, restrito a mídia/imagem | não |
| `tier` | texto de linha única com lista de valores (`gold`, `silver`, `bronze`) | não |
| `products` | referência a produto, em lista | não |

Em Opções, ativa **Ativo/Rascunho** e **Acesso à loja virtual** — sem o segundo o Liquid simplesmente não vê o metaobject, mesmo com entradas cadastradas.

## Usando a section

No editor de temas, adiciona "EC Vendedores" em qualquer template. Dá pra configurar título, subtítulo, rótulo dos produtos, o handle do metaobject (caso use um nome diferente de `store_vendor`), máximo de vendedores e de produtos por card, colunas por linha (desktop de 1 a 6, mobile 1 ou 2), espaço entre cards, cor da borda, cor de fundo do card, padding interno, arredondamento, e fundo/padding da seção toda.

Breakpoints do grid: abaixo de 750px usa a coluna configurada pro mobile, entre 750px e 989px usa no máximo 2 colunas, acima de 990px usa o valor configurado pro desktop.

## Comportamento sem dados

Sem nenhum registro publicado a section não renderiza nada na loja — nem o CSS carrega. Dentro do editor de temas aparece um aviso dizendo que não encontrou entradas, pra quem estiver montando a página saber o que fazer.

Campo vazio não derruba nada: sem foto cai no avatar padrão, sem produto o card simplesmente não mostra a linha de produtos, produto excluído ou despublicado é ignorado na lista. Cor apagada no editor vira transparente (ex: borda apagada = card sem borda visível).

## Limite

O Liquid só devolve as primeiras 50 entradas de um metaobject sem paginação. Pra mais que isso a section precisaria de `paginate`.

## Validação

Primeiro rodei local, sem loja: `shopify theme check` (zero problemas na section e no snippet) e simulei a renderização com liquidjs e metaobjects fake, testando com 0, 6, 9 e 12 registros, mais os casos de borda — nome bem grande (trunca com reticências), vendedor sem foto, sem medalha, sem produtos, sem nome, e produto referenciado que foi excluído.

Depois testei numa loja de desenvolvimento de verdade (`shopif-desafio.myshopify.com`):

- criei a definição do metaobject e cadastrei 6 vendedores pelo Admin, um deles ficou como rascunho — só os 5 ativos aparecem na section, o de rascunho some
- editei diretamente pelo Theme Editor (cor da borda, número de colunas, padding) e a mudança reflete na hora, sem precisar recarregar
- conferi o preview mobile e desktop dentro do editor
- rodei Lighthouse mobile na página publicada, com a section e depois com ela oculta, pra comparar:

| métrica | com a section | sem a section |
| --- | --- | --- |
| Performance | 85 | 87 |
| First Contentful Paint | 1.8s | 1.9s |
| Largest Contentful Paint | 2.1s | 1.9s |
| Total Blocking Time | 440ms | 400ms |
| Cumulative Layout Shift | 0 | 0 |
| Speed Index | 3.7s | 3.5s |

  a diferença é pequena e nem é consistente entre as métricas (o FCP, por exemplo, ficou ligeiramente pior sem a section), o que indica que é ruído normal de execução e não impacto real — faz sentido, já que a section não carrega nenhum JavaScript
- `shopify theme check` continuou sem erros

O teste de "zerar tudo pra rascunho e ver a section sumir da loja publicada" não foi repetido na loja real — já tinha sido coberto na simulação local (0 registros) e o comportamento no código é o mesmo independente de onde roda.

## Preview

```bash
npm i -g @shopify/cli
shopify theme dev --store <sua-loja>.myshopify.com          # preview local com hot reload
shopify theme push --unpublished --store <sua-loja>.myshopify.com   # gera tema não publicado + link de preview
```

Link de preview usado durante os testes: `https://shopif-desafio.myshopify.com/?preview_theme_id=167065780444`

Por ser loja de desenvolvimento, pede a senha de acesso antes de mostrar qualquer coisa — a senha é `peocku`.

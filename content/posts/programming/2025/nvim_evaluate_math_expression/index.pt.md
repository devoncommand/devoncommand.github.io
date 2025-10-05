---
# region: Hugo
# author: [] # [string<Array> | string]
date: 2025-10-05
# expiryDate: 2025-02-07T23:59:59-03:00 # Hugo excludes expired pages, use `--buildExpired` to include them
draft: false
title: 'Neovim - Resolver expressões matemáticas (sem plugins)'
summary: 'Neste post, eu mostro como resolver expressões matemáticas no Neovim usando apenas comandos.' # Refer to: 'https://gohugo.io/content-management/summaries/'
description: 'Resolver expressões matemáticas no Neovim, sem plugins'
tags: ['neovim'] # [string<Array>]
aliases: [] # URL aliases. [string<Array>]
keywords: ['neovim', 'comandos', 'matemática', 'vim', 'calculadora'] # [string<Array>]
categories: ['Programação'] # [string<Array>]
# regionend

# region: hugo-PaperMod
#CanonicalLinkText: 'Originally published at' # Text before the hostname of the canonical URL. [string]
#canonicalURL: 'https://canonical.url/to/page' # Add a canonical URL to posts. [string]
#ShowCanonicalLink: true # Show the hostname of the canonical URL. [true | false]
cover:
  image: 'example.pt.gif' # Add a cover image. [string]
  alt: 'No gif, é demonstrado como utilizando atalhos no teclado podemos calcular quantos segundos há em uma semana' # Alternate text if the image does not load. [string]
  caption: 'Calculando expressão matemática usando combinações no teclado' # Caption for the cover image. [string]
  hidden: false # Hide cover image on the current single page. [true | false]
  relative: false # Use relative path for cover image (used in Hugo Page Bundles). [true | false]
  responsiveImages: false # Enable/disable responsive cover images. [true | false]
#disableShare: false # Hide/show share icons under a page. [true | false]
#ShareButtons: ['linkedin', 'x'] # Customize which share buttons are enabled on the page. [list]
ShowBreadCrumbs: true # Show breadcrumb navigation above single posts/pages. [true | false]
ShowCodeCopyButtons: true # Show/hide code copy button. [true | false]
ShowPostNavLinks: true # Show previous and next post navigation links. [true | false]
ShowReadingTime: true # Show reading time in post metadata. [true | false]
ShowRssButtonInSectionTermList: true # Show RSS button in section, term, and list pages. [true | false]
ShowWordCount: false # Show word count in post metadata. [true | false]
UseHugoToc: false # Use Hugo's default ToC instead of a custom one. [true | false]
#comments: true # Show/hide comments. [true | false]
disableAnchoredHeadings: false # Disable anchored headings. [true | false]
hideFooter: false # Hide the footer text at the end of the page. [true | false]
hideSummary: false # Hide summary displayed on list pages. [true | false]
hidemeta: false # Hide meta elements (date, read-time, author, available translations). [true | false]
robotsNoIndex: false # Prevent a page from being indexed by search engines. [true | false]
searchHidden: false # Hide page from internal search. [true | false]
showtoc: true # Show/hide the Table of Contents. [true | false]
tocopen: false # Keep the Table of Contents open by default on page load. [true | false]
# regionend
---

<!-- @format -->

## Introdução

Se assim como eu você não gosta de ter que instalar plugins no Neovim para coisas que deveriam ser simples, eu te mostro como resolver expressões matemáticas usando uma simples macro e atalho no teclado.

**Observação:** Feito no Neovim v0.11.4, entretanto, o recurso existe a anos e funciona em versões anteriores…

### Neovim tem uma "calculadora" nativa

Abra uma instância do neovim e enquanto estiver no modo `inserir` aperte `<C-r>=` (CTRL + r então =). Você verá que o Neovim irá abrir uma linha de comando onde você pode resolver expressões matemáticas. Você já deve saber onde isso vai dar…

### Calculando expressões matemáticas

Para resolver expressões matemáticas no Neovim, é necessário seguir os mesmos princípios e símbolos utilizados no `JavaScript` e `Python` por exemplo (ou como… qualquer outra linguagem de programação?).

Por exemplo, para calcular o resultado de `28` vezes `13.2`, utiliza-se o símbolo `*`:

```javascript
28 x 13,2 // Errado
28 * 13.2 // Correto
```

O primeiro exemplo é incorreto porque usa `x` ao invés de `*` como símbolo de multiplicação, e `,` para casas decimais ao invés de `.`.

### Atalhos

Você deve estar se perguntando: `Eu preciso abrir a calculadora toda vez que quiser fazer algum cálculo?`. A resposta é `não`. Basta criar um atalho nos arquivos de configuração do Neovim, dentro do `init.lua` ou algum `módulo de atalhos` por exemplo.

#### Criando o atalho

O atalho a seguir estará disponível no modo `visual`. O que ele faz é… basicamente rodar em sequência alguns comandos por você, assim como uma macro.

```lua
vim.keymap.set(
  'v',
  '<leader>g=',
  'c<C-r>=<C-r>-<cr>',
  { desc = 'Calcular expressão matemática (substitui a seleção com o resultado)' }
)
```

Vamos olhar o que cada comando faz…

- **`c` (mudar)**: Deleta o texto, salva no registrador e inicia o modo `inserir`.
- **`<C-r>=` (Abre a calculadora)**: Abre a calculadora na linha de comando.
- **`<C-r>-` (colar)**: Insere o texto na calculadora.
- **`<cr>` (confirma)**: Tecla enter na calculadora do Neovim.

Simples, não? Depois disso, o Neovim irá colocar o resultado da expressão matemática em seu arquivo. Agora, sempre que quiser calcular uma expressão matemática no Neovim, basta teclar `<leader>g=` enquanto estiver com a expressão selecionada.

##### Alguns poréns

Esse atalho tem alguns pontos negativos…

- Funciona somente para expressões em uma única linha (pois usamos `-` como registrador), expressões em múltiplas linhas precisam de outra abordagem.
- O separador de decimais deve ser o `.`, sem vírgulas na expressão (o exemplo mostra intencionalmente o uso incorreto nas casas decimais)
- Talvez você prefira usar `g=` como atalho, reduzindo a quantidade de teclas mas irá indisponibilizar o formatador padrão do Neovim.

## Conclusão

Por que complicar as coisas e "poluir" o seu Neovim para resolver problemas simples? [^simplicidade]

[^simplicidade]: Terry A. Davis — "Um idiota admira a complexidade; um gênio admira a simplicidade." (YouTube, ~18:57) [source: https://youtu.be/gBE6glZNJuU?t=1137].

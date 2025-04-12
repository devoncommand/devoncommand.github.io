---
# region: Hugo
# author: [] # [string<Array> | string]
date: 2025-04-12
# expiryDate: 2025-02-07T23:59:59-03:00 # Hugo excludes expired pages, use `--buildExpired` to include them
draft: false
title: 'Como Resolver Problemas de CORS com Fluig Usando Proxy Reverso no Angular'
summary: 'Dicas essenciais para contornar problemas de CORS ao acessar o Fluig durante o desenvolvimento local com Angular, utilizando proxy reverso com `proxy.conf.json`.' # Refer to: 'https://gohugo.io/content-management/summaries/'
description: 'Este guia apresenta uma solução prática para resolver erros de CORS ao consumir a API do Fluig em ambiente local com Angular. Através da configuração de um proxy reverso com `proxy.conf.json`, é possível redirecionar requisições e evitar a necessidade de alterar as configurações do servidor. As práticas abordadas aqui também são aplicáveis a outras APIs externas que enfrentem o mesmo problema.'
tags: ['cors', 'fluig', 'angular'] # [string<Array>]
aliases: [] # URL aliases. [string<Array>]
keywords: ['Fluig', 'Angular', 'CORS', 'Fluig CORS'] # [string<Array>]
categories: ['Programação'] # [string<Array>]
# regionend

# region: hugo-PaperMod
#CanonicalLinkText: 'Originally published at' # Text before the hostname of the canonical URL. [string]
#canonicalURL: 'https://canonical.url/to/page' # Add a canonical URL to posts. [string]
#ShowCanonicalLink: true # Show the hostname of the canonical URL. [true | false]
# cover:
#   image: 'featured.jpg' # Add a cover image. [string]
#   alt: 'This is the cover image' # Alternate text if the image does not load. [string]
#   caption: 'Caption for image' # Caption for the cover image. [string]
#   hidden: false # Hide cover image on the current single page. [true | false]
#   relative: false # Use relative path for cover image (used in Hugo Page Bundles). [true | false]
#   responsiveImages: true # Enable/disable responsive cover images. [true | false]
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

Durante o desenvolvimento de aplicações que consomem APIs externas, é comum esbarrar em um problema conhecido por muitos: **CORS (Cross-Origin Resource Sharing)**. Se você trabalha com o **Fluig** e desenvolve localmente com Angular, provavelmente já viu aquele erro irritante no console:

> “_Access to XMLHttpRequest at 'https://fluig.seuservidor.com/api/...' from origin 'http://localhost:4200' has been blocked by CORS policy..._”

Mas calma, existe uma forma simples e eficiente de contornar esse obstáculo durante o desenvolvimento, sem precisar alterar a configuração do servidor Fluig: configurar um proxy reverso local no Angular.

## Entendendo o Problema

O CORS é um mecanismo de segurança dos navegadores que impede que uma aplicação web feita em um domínio (ex: `localhost:4200`) faça requisições para outro domínio (ex: `fluig.seuservidor.com`) sem a devida permissão. Isso é ótimo em produção, mas um incômodo durante o desenvolvimento local.

Como não temos (ou não queremos) alterar as configurações do Fluig para liberar origens cruzadas, podemos resolver isso redirecionando as requisições localmente.

## A Solução: Usando `proxy.conf.json`

O Angular CLI permite criar um proxy reverso com apenas um arquivo de configuração. Com isso, ele intercepta suas requisições locais e as redireciona como se estivessem vindo do mesmo domínio do backend. Veja como fazer:

1. Crie o arquivo `proxy.conf.json` na raiz do seu projeto Angular com o seguinte conteúdo:

```json
{
  "/api": {
    "target": "https://fluig.seuservidor.com",
    "secure": true,
    "changeOrigin": true,
    "logLevel": "debug"
  }
}
```

- Explicando cada propriedade:
  - `"/api"`: indica o caminho que você quer interceptar. Tudo que começar com `/api` será redirecionado.
  - `"target"`: o endereço do seu servidor Fluig.
  - `"secure": true`: informa que o target usa HTTPS. Coloque `false` se estiver usando um certificado inválido no Fluig.
  - `"changeOrigin": true`: ajusta o cabeçalho `Origin` da requisição para coincidir com o do target.
  - `"logLevel": "debug"`: útil para depuração, mostra no terminal o que está sendo redirecionado.

2. Agora, inicie o servidor Angular com o proxy ativado:

```bash
ng serve --proxy-config proxy.conf.json
```

3. Pronto! Agora ao fazer chamadas para `/api`, por exemplo `http://localhost:4200/api/public/ecm/dataset`, o Angular irá redirecionar para `https://fluig.seuservidor.com/api/public/ecm/dataset`, sem esbarrar no CORS.

## Conclusão

Essa é uma forma prática e rápida de evitar dores de cabeça com CORS durante o desenvolvimento local com Angular e Fluig. Evita alterações inseguras no servidor e mantém o foco no que realmente importa: codar.

Além disso, essa abordagem funciona com qualquer API externa, não só o Fluig — o que a torna uma ferramenta poderosa no seu arsenal de desenvolvimento.

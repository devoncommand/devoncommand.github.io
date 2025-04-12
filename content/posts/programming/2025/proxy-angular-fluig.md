---
# region: Hugo
# author: [] # [string<Array> | string]
date: 2025-04-12
# expiryDate: 2025-02-07T23:59:59-03:00 # Hugo excludes expired pages, use `--buildExpired` to include them
draft: false
title: 'How to Solve CORS Issues with Fluig Using Reverse Proxy in Angular'
summary: 'Essential tips to overcome CORS issues when accessing Fluig during local development with Angular, using reverse proxy with `proxy.conf.json`.' # Refer to: 'https://gohugo.io/content-management/summaries/'
description: 'This guide presents a practical solution to resolve CORS errors when consuming the Fluig API in a local environment with Angular. By configuring a reverse proxy with `proxy.conf.json`, it is possible to redirect requests and avoid the need to change server settings. The practices discussed here are also applicable to other external APIs facing the same problem.'
tags: ['cors', 'fluig', 'angular'] # [string<Array>]
aliases: [] # URL aliases. [string<Array>]
keywords: ['Fluig', 'Angular', 'CORS', 'Fluig CORS'] # [string<Array>]
categories: ['Programming'] # [string<Array>]
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

## Introduction

During the development of applications that consume external APIs, it is common to encounter a problem known to many: **CORS (Cross-Origin Resource Sharing)**. If you work with **Fluig** and develop locally with Angular, you have probably seen that annoying error in the console:

> “_Access to XMLHttpRequest at 'https://fluig.seuservidor.com/api/...' from origin 'http://localhost:4200' has been blocked by CORS policy..._”

But don't worry, there is a simple and efficient way to overcome this obstacle during development without needing to change the Fluig server configuration: setting up a local reverse proxy in Angular.

## Understanding the Problem

CORS is a browser security mechanism that prevents a web application made in one domain (e.g., `localhost:4200`) from making requests to another domain (e.g., `fluig.seuservidor.com`) without proper permission. This is great in production but a nuisance during local development.

Since we do not have (or do not want) to change the Fluig settings to allow cross-origin requests, we can solve this by redirecting the requests locally.

## The Solution: Using `proxy.conf.json`

The Angular CLI allows you to create a reverse proxy with just a configuration file. With this, it intercepts your local requests and redirects them as if they were coming from the same backend domain. Here's how to do it:

1. Create the `proxy.conf.json` file at the root of your Angular project with the following content:

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

- Explaining each property:
  - `"/api"`: indicates the path you want to intercept. Everything that starts with `/api` will be redirected.
  - `"target"`: the address of your Fluig server.
  - `"secure": true`: informs that the target uses HTTPS. Set to `false` if you are using an invalid certificate on Fluig.
  - `"changeOrigin": true`: adjusts the `Origin` header of the request to match that of the target.
  - `"logLevel": "debug"`: useful for debugging, shows in the terminal what is being redirected.

2. Now, start the Angular server with the proxy enabled:

```bash
ng serve --proxy-config proxy.conf.json
```

3. Done! Now when making calls to `/api`, for example `http://localhost:4200/api/public/ecm/dataset`, Angular will redirect to `https://fluig.seuservidor.com/api/public/ecm/dataset`, without encountering CORS.

## Conclusion

This is a practical and quick way to avoid headaches with CORS during local development with Angular and Fluig. It avoids unsafe changes on the server and keeps the focus on what really matters: coding.

Moreover, this approach works with any external API, not just Fluig — making it a powerful tool in your development arsenal.

---
# region: Hugo
# author: [] # [string<Array> | string]
date: 2025-10-05
# expiryDate: 2025-02-07T23:59:59-03:00 # Hugo excludes expired pages, use `--buildExpired` to include them
draft: false
title: 'Neovim - Evaluate math expressions (without plugins)'
summary: "In this post, I'll show how to evaluate math expressions on Neovim using just commands" # Refer to: 'https://gohugo.io/content-management/summaries/'
description: 'Evaluate math expressions with no plugins on Neovim'
tags: ['neovim'] # [string<Array>]
aliases: [] # URL aliases. [string<Array>]
keywords: ['neovim', 'commands', 'math', 'vim', 'calculator'] # [string<Array>]
categories: ['Programming'] # [string<Array>]
# regionend

# region: hugo-PaperMod
#CanonicalLinkText: 'Originally published at' # Text before the hostname of the canonical URL. [string]
#canonicalURL: 'https://canonical.url/to/page' # Add a canonical URL to posts. [string]
#ShowCanonicalLink: true # Show the hostname of the canonical URL. [true | false]
cover:
  image: 'example.gif' # Add a cover image. [string]
  alt: 'A gif demonstrating the key map evaluating a math expression to calculate how many seconds are in a week' # Alternate text if the image does not load. [string]
  caption: 'Evaluating a math expression using a key map' # Caption for the cover image. [string]
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

## Introduction

If you also hate having to install plugins for everything on Neovim, I will show you how I evaluate math expressions using just a simple macro and a key map.

**Note:** Done on Neovim v0.11.4, tho it's been available for years…

### Neovim has a built-in calculator

Open a Neovim instance, and whilst in `insert` mode hit `<C-r>=` (CTRL + r then =). You may see that, on the command line, an expression calculator will be available. You can see where we are going, right?

### Evaluating math expressions

When evaluating math expressions on Neovim's calculator, you must follow the same symbols you use on `JavaScript` or `Python` (or… all… programming languages?).

For example, if you want to evaluate how much `28` times `13.2` is, you must use the `*` symbol:

```javascript
28 x 13,2 // Wrong
28 * 13.2 // Correct
```

The first example is **incorrect** because it uses `x` instead of `*` for the multiplication symbol and `,` instead of `.` for decimals.

### Key map

You may be asking: `Do I need to open the calculator every time I want to evaluate a math expression?`. The answer is `no`. You can create a key map somewhere on your Neovim config files, like in your `init.lua` or `keymaps module`.

#### Mapping

The following key map is set to be available only on `visual` mode. What it does, is… basically run a set of commands, just like a macro does.

```lua
vim.keymap.set(
  'v',
  '<leader>g=',
  'c<C-r>=<C-r>-<cr>',
  { desc = 'Evaluate math expression (replaces selection with result)' }
)
```

Let's break it down…

- **`c` (change)**: Delete text into a register and start `insert`.
- **`<C-r>=` (open calculator)**: Opens the calculator in the command line.
- **`<C-r>-` (paste)**: Paste into the calculator's input, the deleted text, smaller than one line.
- **`<cr>` (confirm)**: Tells Neovim to press enter on the current command line (calculator).

That's it! Simple, right? After that, Neovim will proceed to paste the evaluated math expression result, into the buffer. Now every time you need to evaluate a math expression, just select it, and hit `<leader>g=` on your keyboard whilst a math expression is visually selected.

##### Caveats

This key map has some caveats…

- Works for selections **under one line** (since it uses the `-` register), multi-line needs a different approach.
- You may prefer to use `g=` for less key presses, but overriding `g=` changes default formatting behavior.

## Conclusion

To fix simple problems, why overcomplicate things and "pollute" your plugins list? [^simplicity]

[^simplicity]: Terry A. Davis — “An idiot admires complexity; a genius admires simplicity.” (YouTube, ~18:57) [source: https://youtu.be/gBE6glZNJuU?t=1137].

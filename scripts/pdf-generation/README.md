# How to generate PDFs from datasheets

To generate PDF from all the datasheets just run (it should work on macOS and Linux at least):

```
npm run pdf-generation
```

**But make sure that you've done following things before:**
- Install all the **dependencies** with `npm install`
- Install **wkhtmltopdf** tool (0.12.4 is used here) for your system from https://wkhtmltopdf.org/downloads.html
- Have **[Montserrat](https://github.com/JulietaUla/Montserrat) and [Fira Mono](https://github.com/mozilla/Fira) fonts** installed in your system (alternative links on Font Squirrel: [Montserrat](https://www.fontsquirrel.com/fonts/montserrat), [Fira Mono](https://www.fontsquirrel.com/fonts/fira-mono); on Google Fonts: [Montserrat](https://fonts.google.com/specimen/Montserrat), [Fira Mono](https://fonts.google.com/specimen/Fira+Mono))


## What Script Does

The script is just a gulp file (`scripts/pdf-generation/pdf-generation-gulpfile.js`) which calls `wkhtmltopdf`.

1. **Copy assets** from `src/assets/`
2. **Prepare styles** (from LESS) for the pages (not for cover and TOC)
3. For each datasheet (markdown file in `src/content/datasheets/`):
    1. **Strips frontmatter** in datasheets. These are the lines between 2 `---` at the top of the markdown.
    2. **Strips web-only sections** in datasheets. Lines between `{{#unless pdf-generation}}` and `{{/unless}} {{!-- pdf-generation --}}` won't be in the PDF.
    3. **Fix relative paths** to assets: `/assets/…` and `{{assets}}/…` → `./assets/…`
    4. **Compile** datasheets **from markdown to HTML**
    5. **Add** general **HTML5 header and footer** around that HTML
    6. **Run `wkhtmltopdf`** to make PDFs of such HTMLs, cover pages, and generate TOC

## Files Structure

```
docs
├── _pdf-datasheets-build    ← temporary folder for compiled datasheets, styles, and copied assets
…
├── scripts
│   ├── pdf-generation
│   │   ├── styles
│   │   │   ├── datasheets.less           ← styles for datasheets' pages
│   │   │   ├── toc.xsl                   ← TOC style and outline
│   │   ├── pdf-generation-gulpfile.js    ← script file
│   │   ├── README.md
│   …
├── src
│   ├── assets
│   │   ├── pdfs
│   │   │   ├── datasheets    ← folder for PDF results
│   ├── content
│   │   ├── datasheets
│   │   │   ├── covers
│   │   │   │   ├── *.html    ← cover pages
│   │   │   │   ├── …         ← resources for cover pages
│   │   │   ├── *.md          ← the datasheets
│   │   …
│   …
…
```

## Datasheets

All content for datasheets is in markdown files in `src/content/datasheets/`. Those markdowns are shared for both web version and PDFs.

**To add a new datasheet**, just add a markdown file to `src/content/datasheets/`.

**To edit a datasheet**, just edit the corresponding markdown file in `src/content/datasheets/`.

Mark sections for the web version only **using special comments**:

```
{{#unless pdf-generation}}
Only web content!
{{/unless}} {{!-- pdf-generation --}}
```

## Page Breaks

To add a page break, just add a line  with `---` (markdown's for `<hr>`). Like that:

```
Some content.

---

Content on new page.

```

## Cover Pages

For each datasheet markdown file (in `src/content/datasheets/`) script will use for a cover page the html-file with the same name in `src/content/datasheets/covers/`.

**To add a cover page**, just add an html-file for it with the same name as datasheet's file.

**To modify the cover page**, just modify the html-file with the same name.

**To add resources** for a cover page, just drop them in the same folder `src/content/datasheets/covers/` and use relative paths.

For now, there is no unified style, so feel free to copy the inlined styles from the existing covers.

## Table of Contents

Table of Contents is generated automatically by `wkhtmltopdf` using headers tags.

To customize the output of TOC (like adding header, or changing styles), modify the `toc.xsl` file in `scripts/pdf-generation/styles/`.

## Changing Pages Styles

Main stylesheet for pages is in `datasheets.less` in `scripts/pdf-generation/styles/`.

Currently, all styles are in that one file, but you can import other files in it. So consider `datasheets.less` as an index file for styles.

## Issues

The script may not work on Windows machines (haven't tested).

## Useful Links

- “Docs” for `wkhtmltopdf` → https://wkhtmltopdf.org/usage/wkhtmltopdf.txt

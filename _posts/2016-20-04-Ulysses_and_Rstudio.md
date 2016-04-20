---
title: "How-to use Ulysses, Rstudio together"
layout: post
---


# How-to use Ulysses, Rstudio together

1. Use Papers for inserting references in Ulysses manuscript (change citation to pandoc)
   - Export folder with references from Papers to a bibtex file
   - Easiest way is probably to use keywords for papers that are used (i.e. keyword is title of paper) and making a smart folder in papers that contains papers matching this keyword
2. Export from Ulysses to markdown
3. Rename md file to Rmd
4. Open in Rstudio

Add the following to the YAML:

```
---
title: "Title of paper"
author: "Name"
date: "data"
output: html_document
csl: apa.csl # must be located in same folder as paper
css: APAStyle.css  # must be located in same folder as paper
bibliography: "references".bib # must be located in same folder as paper
---
```

5. It is not possible to go back from .Rmd to Ulysses gracefully. Better to write additional stuff in Ulysses, then copy this back into the r file (making sure that any extra references used are included in the bibtext file)
6. Tables and figures must either go in-text or in separate documents, not possible to have them after references, these are always added to the end of the file.


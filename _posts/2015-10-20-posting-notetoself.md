# Posting guide

The easiest posting seem to be to make rmarkdown file as usual in rstudio. then check keep md file in settings (advances) using the little symbol next to the knit button. This strips away all yaml, so need to insert:

---
title: "...title goes here..."
layout: post
---

at the top of the page.

Save md file to _post folder after this.

naming: either name the original rmd file as YEAR-MO-DA-name or rename the final md file to this format. 

Committ and push to github.

OH - github doesn't seem to to like tables made using ```kable``
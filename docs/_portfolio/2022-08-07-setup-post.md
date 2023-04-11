---
output: 
  md_document:
    variant: markdown+backtick_code_blocks
    preserve_yaml: TRUE
knit: (function(inputFile, encoding) {
      out_dir <- "_portfolio";
      rmarkdown::render(inputFile,
                        encoding=encoding,
                        output_dir=file.path(dirname(inputFile), out_dir))})
title: Setup Post

---

## github pages guide

I started with the guide here: <https://pages.github.com/>

Here are some things that are important for each post...

```{=html}
<!-- ## Name of the file  -->
```
```{=html}
<!-- The rmarkdown file must be named "YYYY-MM-DD-name-of-post.Rmd". Github pages will automatically title the post "name of post" (with the dashes replaced by spaces). The file name doesn't need to be capitalized, jekyll will capitalize the post title automatically.  -->
```
## File organization

There are three important pieces to get blog posts from Rstudio to
github pages: the rmarkdown file must be located in the `docs` folder,
it must create a markdown file in the `_portfolio` folder and save
images in the `docs/assets` folder.

### Rmarkdown file

When making a new blog post, I edit the code, text and figures in an
rmarkdown file (`.rmd`). I save the `.rmd` file in the `docs` folder.

### Markdown file

In the `.rmd` file header, I specify

``` r
output: 
  md_document:
    variant: markdown+backtick_code_blocks
    preserve_yaml: TRUE
knit: (function(inputFile, encoding) {
      out_dir <- "_portfolio";
      rmarkdown::render(inputFile,
                        encoding=encoding,
                        output_dir=file.path(dirname(inputFile), out_dir))})
title: Setup Post
```

This ensures a few things: 1. the output is a markdown file (`.md`)
which works with github pages 2. the code blocks are recognized when
there are three backticks before and after the blocks 2. the front
matter in the `.rmd` is preserved in the `.md` file 3. the `.md` file is
saved in the `_portfolio` directory folder (which is where pages looks
when it is processing posts) 4. the title is needed and is used as the
link from the main page

### Images

To process images, all images need to be saved to the `assets` folder,
which is located within the `docs` folder. I do this with an r code
block right below the header with the following:

``` r
knitr::opts_chunk$set(fig.path ="/assets/")
```

## Had some issues with figures

This post solved my issues:
<http://www.randigriffin.com/2017/04/25/how-to-knit-for-mysite.html>

I had to change the figure option to this:

``` r
knitr::opts_knit$set(base.dir = "~/gannawag.github.io/docs/", base.url = "/")
knitr::opts_chunk$set(fig.path = "assets/")
```

Need to be sure we are populating the correct `assets` folder (should be
in the project directory).

### Including Plots Example to make sure it works

![](/assets/pressure-1.png)

## Github

Commit only the `.md` and the `assets` folder (not the `.Rmd` file).

## I also needed to change a few settings in the `config.yml` file, the `index.html` and the `_portfolio` pages folder.

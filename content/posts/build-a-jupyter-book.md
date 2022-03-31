---
title: "Build a Jupyter Book"
tags: ["jupyter-book"]
date: "2022-03-30"
---

## Installation

First, I assume you already have Python `>= 3.8` and Git installed.

Now install `jupyter-book` package as follows

```bash
pip install -U jupyter-book
```

Double-check if the installation was done correctly

```bash
jupyter-book --help
```

## Process of building a book

1. Create your content: folders, files, and configuration
1. Build your book: convert your content to HTML or PDF
1. Publish your book: bring it online

## Create a book

You might have multiple books. Thus, create a folder for all of your book projects first, for example

```bash
D:/projects/git/books/
```

Open a Git Bash terminal and navigate inside `books/`

```bash
cd D:/projects/git/books/
```

Now create a new book for example `my_book`

```bash
jb create my_book
```

This will create a minimal template for your book that you can start editing.

Read more [here](https://jupyterbook.org/start/create.html)

## Anatomy of a book

There are three things that you need to build a book

- A configuration file: `_config.yml`
- A table of contents file: `_toc.yml`
- And your book's content: `.md` and `.ipynb` files

Here is a sample structure

```bash
my_book/
	|__ _config.yml
	|__ _toc.yml
	|__ _static/
	|__ 000_intro.md
	|__ 001_chapter_1.ipynb
	|__ 002_chapter_2.ipynb
	|__ logo.png
	|__ requirements.txt

```

### Config file

All of the configuration for your book is in `_config.yml`. Example

```bash
title: "Python tutorials"
author: "Tue Nguyen"
logo: logo.png
execute:
  execute_notebooks: force
```

### Table of contents

The table of contents defines the structure of your books (which files to include and their order). The configuration for TOC is in  `_toc.yml`. Example

```bash
format: jb-book
root: 000_intro
chapters:
- file: 001_chapter_1
- file: 002_chapter_2
- file: 003_chapter_3
```

### Book content

Your content is a collection of `.md` or `.ipynb` files. Read more about possible kinds of content [here](https://jupyterbook.org/file-types/index.html)

Rules for all content files

- A file must have a single title (the first line start with `#`)
- Headers should increase linearly (for example, no jumping straight from `#` to `###`)

### Static

Put your images inside `_static/img`. If not, your images will not be rendered.


## Build your book

Inside the root directory of your book (i.e., `my_book/`, run the following command

```bash
jb build .
```

Your HTML version of your book will be generated in `_build/html`. You can go inside it and preview some HTML files.


## Publish your book

This section is a guide for deploying your book on Github only.

- Make your local repo a git repo and make your first commit

```bash
git init
```

- Add a `.gitignore` file. Example content

```bash
.ipynb_checkpoints
*/.ipynb_checkpoints/*
_build
```

- Make your first commit

```bash
git status
git add .
git commit -m "Initial commit"
```

- Sync this repo with your local repo and push your code

```bash
git branch -M main
git remote add origin path/to/your/github/account/my_book.git
```

- Auto build on push with Github actions by creating `.gihub/workflows/gh_pages.yml` with the following content

```bash
name: deploy-book

# Only run this when the master branch changes
on:
  push:
    branches:
    - main
    # If your git repository has the Jupyter Book within some subfolder next to
    # unrelated files, you can make this run only if a file within that specific
    # folder has been modified.
    #
    # paths:
    # - some-subfolder/**

# This job installs dependencies, builds the book, and pushes it to `gh-pages`
jobs:
  deploy-book:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2

    # Install dependencies
    - name: Set up Python 3.8
      uses: actions/setup-python@v2
      with:
        python-version: 3.8

    - name: Install dependencies
      run: |
        pip install -r requirements.txt
    # Build the book
    - name: Build the book
      run: |
        jupyter-book build .
    # Push the book's HTML to github-pages
    - name: GitHub Pages action
      uses: peaceiris/actions-gh-pages@v3.6.1
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./_build/html
```


- Push your book on Github

```bash
git push -u origin main
```

- Go to your repo and access `Settings > Pages > Source` and choose `gh-pages` for branch option and `(root)` for folder option.

## Structure of a book

Here is a basic structure

```bash
# _config.yml
format: jb-book
root: index
chapters:
- file: path/to/chapter1
- file: path/to/chapter2
```

You can organize your chapters into parts


```bash
# _config.yml
format: jb-book
root: index
parts:
  - caption: Name of Part 1
    chapters:
    - file: path/to/part1/chapter1
    - file: path/to/part1/chapter2
  - caption: Name of Part 2
    chapters:
    - file: path/to/part2/chapter1
    - file: path/to/part2/chapter2
      sections:
      - file: path/to/part2/chapter2/section1
```

My advice is to use chapters, not sections. Keep your book as flat as possible.

Read more [here](https://jupyterbook.org/structure/sections-headers.html#toc-structure)


## Other notes

### Version control
You should use separate branches for `source` and `build`. Best practices

- Add `_build` to `.gitignore` on your `main` branch
- Push output of `_build` to `gh-pages`

### Caching

To force Jupyter Book to re-build a particular page, you can delete the `_build/html` folder or run a full re-build

```bash
jb build --all .
```

### Citations

Read more [here](https://jupyterbook.org/tutorials/references.html)

## Reference

https://jupyterbook.org
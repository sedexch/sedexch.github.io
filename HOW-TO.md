# Overview
This site contains the documentation in Markdown format based on MkDocs (https://www.mkdocs.org/) and Material Theme for MkDocs (https://squidfunk.github.io/mkdocs-material/).


# How to serve locally

1. cd to the folder with the mkdocs.yml file (project root)

2. sudo docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material

3. See your auto-hot-loaded site at http://127.0.0.1:8000 


# How to publish

The publication to GitHub Pages can be done via two different ways (see https://squidfunk.github.io/mkdocs-material/publishing-your-site/):
- build the static site locally (e.g. using the docker image) and push the html site (folder /docs/site) to GitHub
- push the rpository and let GitHub do the publication based on a GitHub Actions


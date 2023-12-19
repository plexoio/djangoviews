## Setting Up Your Project

???+ note "CLI/Bash"
    Please note how we make use of Gitpod CLI (Command-line interface) or Bash throughout the process.

### Initial Setup

1. **Use Code Institute's Gitpod Full Template**: Use the template from Code Institute found at [Gitpod Full Template](https://github.com/Code-Institute-Org/gitpod-full-template) to initialize your Gitpod workspace.

    **Note**: Don't forget to install Django to avoid specific errors such as `ValueError: ZoneInfo keys may not be absolute paths, got: /UTC`.

    ```bash
    pip3 install 'django<4' gunicorn psycopg2
    # `gunicorn` & `psycopg2` are optional
    ```

### Installing MkDocs and Dependencies

1. **Install MkDocs**: To create your project documentation.

    ```bash
    pip install mkdocs
    ```

2. If you don't see the `docs` folder automatically created then you need to initialize mkdocs manually:

    ```bash
    mkdocs new .
    ```

3. **Install MkDocs Material**: This is the theme used for MkDocs.

    ```bash
    pip install mkdocs-material

    # Do not forget
    pip3 freeze > requirements.txt
    ```

4. **Test MkDocs**: Ensure everything is working as expected, if not, reinstall Django, mkdocs & mkdocs-material.

    ```bash
    mkdocs serve
    ```
5. **Important**: Remove `.github/` from the `.gitignore` file. It is important to set up the docs with Github Pages:

    ```bash
    core.Microsoft*
    core.mongo*
    core.python*
    env.py
    __pycache__/
    *.py[cod]
    node_modules/
    cloudinary_python.txt
    ```

6. **Push Changes to GitHub**

    ```bash
    git add .
    git commit -m "Initial Commit"
    git push
    ```

## Setting Up GitHub Actions

### Create GitHub Actions Workflow

1. **Create `.github` Folder**: This folder will contain your GitHub workflows.

    ```bash
    mkdir .github
    ```

2. **Navigate to `.github` Folder**

    ```bash
    cd .github
    ```

3. **Create `workflows` Folder**: This folder will contain your workflow files.

    ```bash
    mkdir workflows
    ```

4. **Go Back to Main Directory**

    ```bash
    cd ..
    ```

5. **Create `ci.yml` File**: This file defines your CI (Continuous Integration)/CD (Continuous Deployment/Delivery) pipeline.

    ```bash
    touch ci.yml
    ```

    **Add the Following Content to `ci.yml`:**

    ```yaml
    name: ci
    on:
      push:
        branches:
          - master
          - main
    permissions:
      contents: write
    jobs:
      deploy:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v3
          - uses: actions/setup-python@v4
            with:
              python-version: 3.x
          - uses: actions/cache@v2
            with:
              key: ${{ github.ref }}
              path: .cache
          - run: pip install mkdocs-material
          - run: pip install pillow cairosvg
          - run: mkdocs gh-deploy --force
    ```

6. **Move `ci.yml` to `workflows` Directory**: This makes sure GitHub Actions recognizes your workflow.

    ```bash
    mv ci.yml .github/workflows/
    ```

7. **Push Changes to GitHub**

    ```bash
    git add .
    git commit -m "Setup GitHub Actions"
    git push
    ```

### Final Configuration on GitHub

1. **Navigate to Your Repository's Settings**: Then go to the "Pages" section.
2. **Configure Build and Deployment**: Under 'Build and Deployment', select 'Deploy from a branch' then choose the `source` as `'gh-pages'` **do not change** the `/ (root)` and click the 'Save' button.
3. **Find URL**: Go back to GitHub Actions and check the process `pages build and deployment ` once it is completed (green). Access it to find your URL at the `build-deploy` level.

## Style Your Documentation
Enhance your documentation with the following sample code by updating the `mkdocs.yml` file:

- [DjangoViews Mkdocs Material Style](https://github.com/plexoio/djangoviews/blob/main/mkdocs.yml)

Your GitHub Actions workflow should start automatically. It usually takes a few minutes to deploy. You can check the progress in the "Actions" tab on your GitHub repository. Once the action is complete, your MkDocs documentation will be available on your GitHub Pages URL.

### Logo & Favicon

First, ensure that you have created a folder named `assets/` within the mkdocs folder. This should be the subfolder where `index.md` is located, not the main one. Within the `assets/` folder, create additional subfolders, such as `img/`. Your goal is to have the path  `docs/assets/img/logo.png` for your logo and favicon.

Next, go to your `mkdocs.yml` file and add the following lines under the `theme:` entry:

```yaml
theme:
  logo: assets/img/logo.png
  favicon: assets/img/logo.png
```

## Update Mkdocs & Mkdocs Material

If you want to update all extensions please use the following code:

```bash
pip install --upgrade mkdocs mkdocs-material pymdown-extensions
```

## Install MathJax

MathJax and KaTeX are two popular libraries for displaying mathematical content in browsers. Although both libraries offer similar functionality, they use different syntaxes and have different configuration options. This documentation site provides information on how to integrate them with Material for MkDocs easily. For more visit [Mkdocs Material](https://squidfunk.github.io/mkdocs-material/reference/math/?h=math#mathjax-mkdocsyml)

MathJax is a powerful and flexible library that supports multiple input formats, such as LaTeX, MathML, AsciiMath, as well as various output formats like HTML, SVG, MathML. To use MathJax within your project, add the following lines to your mkdocs.yml:

- First, add this Javascript code:

```javascript
// LOCATION: docs/javascripts/mathjax.js

window.MathJax = {
  tex: {
    inlineMath: [["\\(", "\\)"]],
    displayMath: [["\\[", "\\]"]],
    processEscapes: true,
    processEnvironments: true
  },
  options: {
    ignoreHtmlClass: ".*|",
    processHtmlClass: "arithmatex"
  }
};

document$.subscribe(() => { 
  MathJax.typesetPromise()
})
```

- Second, add this yml code:

```yaml
# LOCATION: mkdocs.yml

markdown_extensions:
  - pymdownx.arithmatex:
      generic: true

extra_javascript:
  - javascripts/mathjax.js
  - https://polyfill.io/v3/polyfill.min.js?features=es6
  - https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js
```

### Bug - ZoneInfo

To avoid the `ValueError: ZoneInfo keys may not be absolute paths, got: /UTC` please install Django as described above.
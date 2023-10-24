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

2. **Install MkDocs Material**: This is the theme used for MkDocs.

    ```bash
    pip install mkdocs-material

    # Do not forget
    pip3 freeze > requirements.txt
    ```

3. **Test MkDocs**: Ensure everything is working as expected, if not, reinstall Django, mkdocs & mkdocs-material.

    ```bash
    mkdocs serve
    ```

---

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

    ```yml
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
2. **Configure Build and Deployment**: Under 'Build and Deployment', select 'Deploy from a branch' then choose the source as 'gh-pages' and click the 'Save' button.

### Style Your Documentation
Enhance your documentation with the following sample code:

- [DjangoViews Mkdocs Material Style](https://github.com/plexoio/djangoviews/blob/main/mkdocs.yml)

Your GitHub Actions workflow should start automatically. It usually takes a few minutes to deploy. You can check the progress in the "Actions" tab on your GitHub repository. Once the action is complete, your MkDocs documentation will be available on your GitHub Pages URL.

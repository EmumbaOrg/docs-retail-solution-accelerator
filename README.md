# Workshop Guide
The current repository is instrumented with a `docs/workshop/` folder that contains the step-by-step lab guide for developers, covering the entire workflow from resource provisioning to ideation, evaluation, deployment, and usage.

You can view the workshop directly from this source by running the [MKDocs](https://www.mkdocs.org/) pages locally:

1. Install the `mkdocs-material` package

    ```bash
    pip install mkdocs-material
    ```

2. Run the `mkdocs serve` command from the `workshop` folder

    ```bash
    cd docs/workshop
    mkdocs serve -a localhost:7000
    ```

This should run the dev server with a preview of the workshop guide on the specified local address. Simply open a browser and navigate to `http://localhost:7000` to view the content.

(Optional) If you want to deploy the workshop guide to a live site, you can use the `mkdocs gh-deploy` command to push the content to a GitHub Pages site.
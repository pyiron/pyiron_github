# pyiron_github
A centralized location for our GitHub actions to perform CI on python modules.
It includes both custom actions, which we strive to make reusable everywhere (but whose default values are optimized for our repositories), and custom reusable workflows that are tuned specifically for our other pyiron repositories.

## Expected repository structure and defaults

To make full use of default values with these actions, your repository should have the following structure:

```
your_module/
|-- .ci_support/
|     |-- environment.yml  # The conda environment file for your module
|-- docs/  # Necessary files for sphinx to built HTML documentation
|     |-- conf.py
|     |-- index.rst
|-- !environment.yml  # You MUST NOT have 'environment.yml' in your repo root -- our actions write to this location
|-- tests/  # The directory containing your unittest test files
|-- your_module/  # The directory for your python module
|     |-- _version.py  # We will assume you are using versioneer and will ignore this when calculating coverage
```

By default, some of our actions add to the environment, e.g. `build-docs` uses the file `.support/environment-docs.yml` in this repository to add `nbsphinx` to the environment with the `standard-docs-env-file` input argument.
In these situations you can always add additional files to the environment in the `env-files` argument, or stop these "standard" modules from getting added by overriding these arguments with an empty string.

Similarly, other defaults like where the docs or notebooks directories are, or even where the final environment file resides can always be overriden for each action.

## Workflows

Unlike the actions, reusable workflows in the `.github/workflows` directory enforce expectations about repository structure and lean heavily on the default values.
Some properties, such as which env files to use, are still exposed.

These workflows are named based on the expected `on` values for triggering the workflow and ought to be applicable to any pyiron module.

Further, various workflows rely on the following repository secrets being defined for your repository:
- `CODACY_PROJECT_TOKEN`
- `DEPENDABOT_WORKFLOW_TOKEN`
- `PYPI_PASSWORD` (Organization-wide)
- `GITHUB_TOKEN` (Don't worry about this, GitHub will produce it automatically)

Thankfully, if you're running these workflows for a pyiron-organization module, all you need to do in your calling workflow is set `secrets: inherit`.

Putting it all together, we get caller scripts that look like this:

```yaml
# This runs jobs which pyiron modules should run on pushes or PRs to main

name: Push-Pull-main

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  pyiron_github:
    uses: pyiron/pyiron_github/.github/workflows/push-pull-main.yml@main
    secrets: inherit
```

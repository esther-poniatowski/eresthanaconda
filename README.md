# eresthina-conda-channel

Personal Conda channel for managing and distributing internal packages through static hosting.

## Features

- Maintains platform-specific build artifacts in Conda-compatible formats.
- Distributes static package metadata required for dependency resolution by the Conda package manager.
- Supports automated build and deployment via continuous integration workflows.

## Hosting and Distribution

This repository is configured for static hosting via GitHub Pages. To use it as a Conda channel:

- **Via Conda Configuration**: Add the channel to the Conda configuration:

  ```sh
  conda config --add channels https://esther-poniatowski.github.io/eresthina-conda-channel
  ```

- **Via Environment Specification**: Alternatively, specify the channel in the `environment.yaml` file of a Conda environment:

  ```yaml
  channels:
    - https://esther-poniatowski.github.io/eresthina-conda-channel
    - conda-forge
    - defaults
  dependencies:
    - <package-name>
  ```


## Package Management and Deployment

This repository supports automated publication of Conda packages using continuous integration workflows.

To enable automated publication of Conda packages to this channel:

1. Define a Conda Recipe: 
   Within the source repository of the package to be built, create a `conda.recipe/` directory containing:

   - `meta.yaml`: Package metadata (required).
   -  `build.sh` or `bld.bat` (optional): Platform-specific build scripts.

3. Configure GitHub Repository Secrets: 
   In the package repository settings, add the following repository secrets:

   - `GH_TOKEN`: GitHub Personal Access Token with "repo" write permissions to the `eresthina-conda-channel` repository.
   - `GH_USERNAME`: GitHub username associated with the token, i.e. `esther-poniatowski`.

4. Implement a GitHub Action Workflow:
   Define a workflow (e.g., `.github/workflows/conda-publish.yml`), following the template provided in `templates/conda-publish.yml`. 
   This workflow must:

   - Build the package for the required platforms using `conda-build`.
   - Copy the resulting archives to a clone of this repository.
   - Update platform metadata in the channel using `conda index`.
   - Commit and push the new package archives and metadata files to this repository using the credentials provided via secrets.

## Repository Structure

The repository complies with the canonical directory and metadata layout expected by the Conda package manager.

Each supported platform defines a subdirectory named according to the standard Conda platform tags (e.g. `linux-64/`, `osx-64/`, `win-64/`). Each directory contains the following components:

- `repodata.json`, `repodata.json.bz2`: Metadata files listing all available packages and their respective dependency specifications.
- `.cache/cache.db`: SQLite cache to accelerate metadata loading.
- `<package>-<version>-<build>.tar.bz2`: Binary package archives for the corresponding platform.

The root directory includes:
- `templates/`: Models of reusable GitHub workflows.
- `.nojekyll`: Flag file that disables GitHub Pages' default Jekyll processing to ensure correct serving of files with underscores and unaltered directory layout.

```tree
eresthina-conda-channel/
├── README.md
├── .nojekyll
├── linux-64/
│   ├── repodata.json
│   ├── repodata.json.bz2
│   ├── .cache/
│   │   └── cache.db
│   └── --.tar.bz2
├── osx-64/
│   └── ...
├── win-64/
│   └── ...
└── templates/
    └── conda-publish.yml
```

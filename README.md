# EresthAnaconda

Personal Conda channel for managing and distributing internal packages through static hosting.

## Features

- Distributes platform-independent and platform-specific build artifacts and metadata for dependency resolution by the Conda package manager.
- Provides static hosting via GitHub Pages.
- Supports automated build and deployment via continuous integration workflows.

## Package Distribution

To fecth packahes from this Conda channel:

- **Via Conda Configuration**: Add the channel to the channel configuration of a Conda environment.

  ```sh
  conda config --add channels https://esther-poniatowski.github.io/eresthanaconda
  ```

- **Via Environment Specification**: Specify the channel in an `environment.yaml` file of a Conda environment.

  ```yaml
  channels:
    - https://esther-poniatowski.github.io/eresthanaconda
    - conda-forge
    - defaults
  dependencies:
    - <package-name>
  ```

- **Via Direct Install**: Pass the channel to the installation command option.
  
  ```sh
  conda install <package-name> -c https://esther-poniatowski.github.io/eresthanaconda
  ```

## Package Deployment

This repository supports automated publication of Conda packages using continuous integration workflows.

To implement automated publication of Conda packages to this channel:

1. **Define a Conda Recipe**: 
   Within the source repository of the package to be built, create a `conda.recipe/` directory containing at least a `meta.yaml` metadata specification.

3. **Configure GitHub Repository Secrets**: 
   In the package repository settings, add the following repository secrets:

   - `CHANNEL_TOKEN`: GitHub Personal Access Token to the `eresthanaconda` repository (with specific write permissions).
   - `CHANNEL_USERNAME`: GitHub username associated with the token, i.e. `esther-poniatowski`.

4. Implement a GitHub Action Workflow:
   Define a workflow (e.g., `.github/workflows/publish.yml`), following the appropriate template provided in `templates/` (platform-specific or "noarch"). 
   This workflow must:

   - Build the package for the required platforms using `conda-build`.
   - Copy the resulting archives to a clone of this repository.
   - Update platform metadata in the channel using `conda-index`.
   - Commit and push the new package archives and metadata files to the channel repository using the credentials provided via secrets.

## Repository Structure

The repository complies with the canonical directory and metadata layout expected by the Conda package manager.

Each supported platform defines a subdirectory named according to the standard Conda platform tags (e.g. `noarch`, `linux-64/`, `osx-64/`, `win-64/`). Each directory contains the following components:

- `repodata.json`, `repodata.json.bz2`: Metadata files listing all available packages and their respective dependency specifications.
- `.cache/cache.db`: SQLite cache to accelerate metadata loading.
- `<package>-<version>-<build>.tar.bz2`: Binary package archives for the corresponding platform.

The root directory includes:

- `templates/`: Models of reusable GitHub workflows.
- `.nojekyll`: Flag file that disables GitHub Pages' default Jekyll processing to ensure correct serving of files with underscores and unaltered directory layout.

```tree
eresthanaconda/
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

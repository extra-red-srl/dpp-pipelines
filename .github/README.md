# DPP Pipelines

This repository contains reusable GitHub Actions workflows.

## Node.js Workflows

### Node.js Test Workflow

The Node.js test workflow runs automated tests on pull requests to ensure code quality and functionality.

#### Workflow Steps

1. **Checkout code** - Retrieves the source code from the repository
2. **Setup Node.js environment** - Configures the specified Node.js version
3. **Cache Node dependencies** - Caches npm dependencies to speed up future builds
4. **Install Dependencies** - Installs project dependencies using `npm ci`
5. **Run tests** - Executes Angular tests using `ng test` with Chrome headless browser

#### Test Workflow Inputs

| Variable Name | Description | Default Value |
|---------------|-------------|---------------|
| `node_version` | Node.js version to use for testing | `22.15` |

### Node.js Build and Release Workflow

The Node.js build and release workflow handles building, packaging, and releasing Node.js applications with Docker support.

#### Build and Release Steps

1. **Checkout code** - Retrieves the source code from the repository
2. **Setup Node.js environment** - Configures the specified Node.js version
3. **Cache Node dependencies** - Caches npm dependencies to optimize build time
4. **Extract name and version** - Extracts artifact name and version from package.json
5. **Install Dependencies** - Installs project dependencies using `npm ci`
6. **Run build** - Builds the Angular application using `ng build`
7. **Zip build artifacts** - Creates a compressed archive of build outputs
8. **Log in to GitHub Container Registry** - Authenticates with the container registry
9. **Build and push Docker image** - Builds and publishes Docker image with tags
10. **Create GitHub Release** - Creates a new release with changelog and artifacts
11. **Upload artifacts to release** - Attaches the zipped build artifacts to the release

#### Build and Release Inputs

| Variable Name | Description | Default Value |
|---------------|-------------|---------------|
| `node_version` | Node.js version to use for building | `22.15` |
| `output_path` | Build output directory path | `./dist` |
| `zipped_paths` | Space-separated list of paths to include in zip | `./dist ./deploy` |
| `image_name` | Docker image name | `${{ github.repository }}` |
| `image_tag` | Docker image tag | `${{ github.ref_name }}` |
| `image_registry` | Docker registry URL | `ghcr.io` |
| `dockerfile_path` | Path to Dockerfile | `./Dockerfile` |

## Quarkus Workflows

### Quarkus Test Workflow

The Quarkus test workflow runs Maven tests on pull requests to ensure code quality and functionality.

#### Quarkus Test Steps

1. **Checkout code** - Retrieves the source code from the repository
2. **Set up Java** - Configures the specified Java version and distribution
3. **Set up Maven** - Configures the specified Maven version
4. **Cache Maven dependencies** - Caches Maven dependencies to speed up future builds
5. **Run tests** - Executes Maven tests using `mvn test`

#### Quarkus Test Configuration

| Variable Name | Description | Default Value |
|---------------|-------------|---------------|
| `JAVA_VERSION` | Java version to use for testing | `21` |
| `JAVA_DISTRIBUTION` | Java distribution to use | `temurin` |
| `MAVEN_VERSION` | Maven version to use | `3.9.6` |

### Quarkus Build and Release Workflow

The Quarkus build and release workflow handles building, packaging, and releasing Quarkus applications with Docker support.

#### Quarkus Build and Release Steps

1. **Checkout code** - Retrieves the source code from the repository
2. **Set up Java** - Configures the specified Java version and distribution
3. **Set up Maven** - Configures the specified Maven version
4. **Cache Maven dependencies** - Caches Maven dependencies to optimize build time
5. **Extract artifactId and version** - Extracts artifact information from pom.xml
6. **Run build** - Builds the Quarkus application using `mvn clean package`
7. **Log in to GitHub Container Registry** - Authenticates with the container registry
8. **Build and push Docker image** - Builds and publishes Docker image with tags
9. **Create GitHub Release** - Creates a new release with changelog and artifacts
10. **Upload JAR artifact to release** - Attaches the JAR artifact to the release

#### Quarkus Build and Release Configuration

| Variable Name | Description | Default Value |
|---------------|-------------|---------------|
| `JAVA_VERSION` | Java version to use for building | `21` |
| `JAVA_DISTRIBUTION` | Java distribution to use | `temurin` |
| `MAVEN_VERSION` | Maven version to use | `3.9.6` |
| `IMAGE_NAME` | Docker image name | `${{ github.repository }}` |
| `IMAGE_TAG` | Docker image tag | `${{ github.ref_name }}` |
| `IMAGE_REGISTRY` | Docker registry URL | `ghcr.io` |
| `DOCKERFILE_PATH` | Path to Dockerfile | `./src/main/docker/Dockerfile.jvm` |

## Usage Examples

### Using Node.js Workflows

```yaml
# Example usage for Node.js test workflow
name: 'Node - Test'

on:
  pull_request:
    branches: ['*']

jobs:
  shared-job:
    uses: extra-red-srl/dpp-pipelines/.github/workflows/node-test.yml@main
    with:
      node_version: '22.15'

```

```yaml
# Example usage for Node.js build and release workflow
name: 'Node - Build and Release'

on:
  push:
    tags:
      - '*'

jobs:
  shared-job:
    uses: extra-red-srl/dpp-pipelines/.github/workflows/node-build-release.yml@main
    with:
      node_version: '22.15'
      output_path: './dist'
      zipped_paths: './dist ./deploy'
      image_name: '${{ github.repository }}'
      image_tag: '${{ github.ref_name }}'
      image_registry: 'ghcr.io'
      dockerfile_path: './Dockerfile'

```

### Using Quarkus Workflows

```yaml
# Example usage for Quarkus test workflow
name: 'Quarkus - Test'

on:
  pull_request:
    branches: ['*']

jobs:
  shared-job:
    uses: extra-red-srl/dpp-pipelines/.github/workflows/quarkus-test.yml@main
    with:
      java_version: '21'
      java_distribution: 'temurin'
      maven_version: '3.9.6'

```

```yaml
# Example usage for Quarkus build and release workflow
name: 'Quarkus - Build and Release'

on:
  push:
    tags:
      - '*'

jobs:
  shared-job:
    uses: extra-red-srl/dpp-pipelines/.github/workflows/quakrus-build-release.yml@main
    with:
      java_version: '21'
      java_distribution: 'temurin'
      maven_version: '3.9.6'
      image_name: '${{ github.repository }}'
      image_tag: '${{ github.ref_name }}'
      image_registry: 'ghcr.io'
      dockerfile_path: './src/main/docker/Dockerfile.jvm'
```

## Prerequisites

### For Node.js Workflows

- `package.json` file in the repository root
- Angular CLI project structure
- Dockerfile for containerization
- Proper test configuration

### For Quarkus Workflows

- `pom.xml` file in the repository root
- Quarkus application structure
- Dockerfile in `./src/main/docker/` directory
- Proper test configuration

## Security Considerations

All workflows use the `GITHUB_TOKEN` for authentication with GitHub Container Registry and creating releases. No additional secrets are required unless customizing the workflows for external services.

## Contributing

When contributing to these workflows, please ensure:

- All changes are backwards compatible
- Documentation is updated accordingly
- Workflows are tested with representative projects

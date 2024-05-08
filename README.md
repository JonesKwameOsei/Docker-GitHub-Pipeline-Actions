# Docker-GitHub-Pipeline-Actions
Automatically build Docker images with GitHub Actions
# Docker-GitHub-Pipeline-Actions

## Overview
This project demonstrates how to automatically build Docker images using GitHub Actions. By integrating Docker and GitHub Actions, DevOps engineers can create a seamless **CI/CD (Continuous Integration/Continuous Deployment)** pipeline for projects, ensuring that Docker images are built and pushed to a registry whenever changes are made by developers to the codebase.

## Step-by-Step Process

### 1. Create a GitHub Repository
Begin by creating a new GitHub repository for your project. This will be the central location for your code, configurations, and the GitHub Actions workflow.<p>

For step by step process of creating a github repo from thr terminal, check this [project](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes)
### 2. Set up the Docker Build Workflow
In your GitHub repository, create a new folder named `.github/workflows`. This is where you'll define your GitHub Actions workflow.

Inside the `workflows` folder, create a new file named `docker-build.yml`. This file will contain the configuration for your Docker build workflow.

```yaml
name: Docker Build

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:

  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1
      
    - name: Login to GitHub Packages
      uses: docker/login-action@v1
      with:
        registry: ghcr.io
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
        
    - name: Build and push
      uses: docker/build-push-action@v2
      with:
        context: .
        push: true
        tags: ghcr.io/${{ github.repository }}:${{ github.sha }}
```

This workflow will be triggered on push and pull request events to the `main` branch. It performs the following steps:

1. Checks out the repository code.
2. Sets up the Docker Buildx environment for multi-architecture builds.
3. Logs in to the GitHub Container Registry (ghcr.io) using the `GITHUB_TOKEN` secret.
4. Builds the Docker image and pushes it to the GitHub Container Registry, tagging it with the current commit SHA.

### 3. Configure Docker Build Options
In the root of your project, create a `Dockerfile` that defines the build instructions for your Docker image. This file should include the necessary steps to build and package your application.

Example `Dockerfile`:

```Dockerfile
FROM node:14-alpine
WORKDIR /app
COPY . .
RUN npm ci
CMD ["npm", "start"]
```

### 4. Enable GitHub Packages
To store your Docker images, you'll need to enable GitHub Packages for your repository. Go to your repository settings, navigate to the "Packages" section, and enable GitHub Packages.

### 5. Run the GitHub Actions Workflow
Commit and push your changes to the `main` branch of your GitHub repository. This will trigger the `docker-build.yml` workflow, which will automatically build and push your Docker image to the GitHub Container Registry.

You can monitor the progress and check the status of the workflow run in the "Actions" tab of your GitHub repository.

## Conclusion
By leveraging GitHub Actions, you can seamlessly integrate Docker into your development pipeline. This approach allows you to automatically build and push Docker images to a registry, ensuring that your deployments are consistent and up-to-date with the latest changes to your codebase. This setup provides a reliable and efficient way to manage your Docker-based applications.

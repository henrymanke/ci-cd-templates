# CI/CD Templates

This repository provides template CI/CD pipelines demonstrating best practices with GitHub Actions and GitLab CI. It includes:

## Repository Structure

```
ci-cd-templates/
├── .github/
│   └── workflows/
│       ├── build-and-test.yml
│       └── deploy.yml
├── gitlab-ci.yml
├── scripts/
│   ├── deploy.sh
│   └── test.sh
├── src/
│   └── example-app/
│       ├── Dockerfile
│       └── app.py
└── README.md
```

## Getting Started

### Prerequisites

* Docker
* Python 3.x
* (Optional) Kubernetes cluster and `kubectl`
* Access to GitHub or GitLab

### Quick Start

1. Clone the repository

   ```bash
   git clone https://github.com/henrymanke/ci-cd-templates.git
   cd ci-cd-templates
   ```
2. Review the workflows in `.github/workflows` and `gitlab-ci.yml`.
3. Customize `scripts/deploy.sh` and `scripts/test.sh` for your environment.

## GitHub Actions Workflows

### build-and-test.yml

```yaml
name: Build and Test

on:
  push:
    branches:
      - main
      - develop
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.x'
      - name: Build Docker image
        run: docker build -t example-app:latest ./src/example-app
      - name: Run tests
        run: |
          cd src/example-app
          pytest
```

### deploy.yml

```yaml
name: Deploy to Kubernetes

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/checkout@v3
      - name: Set up kubectl
        uses: azure/setup-kubectl@v3
        with:
          version: 'stable'
      - name: Deploy to cluster
        run: |
          kubectl apply -f k8s/
```

## GitLab CI (gitlab-ci.yml)

```yaml
stages:
  - build
  - test
  - deploy

build-job:
  stage: build
  script:
    - docker build -t example-app:latest ./src/example-app

test-job:
  stage: test
  script:
    - cd src/example-app
    - pytest

deploy-job:
  stage: deploy
  script:
    - bash scripts/deploy.sh
  only:
    - main
```

## Scripts

* **scripts/deploy.sh**: Environment-specific deployment logic (e.g., Docker push, Helm chart upgrade)
* **scripts/test.sh**: Additional test automation scripts

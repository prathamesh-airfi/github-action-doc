# GitHub Actions Workflow: CI Example

## Overview

This document explains a sample GitHub Actions configuration for setting up a Continuous Integration (CI) pipeline. It covers key components such as workflow, jobs, steps, caching, secrets, and notifications.

## GitHub Actions Keywords Hierarchy

### 1. **Workflow**

- **name**: Defines the name of the workflow.
- **on**: Specifies the events that trigger the workflow (e.g., `push`, `pull_request`).

### 2. **Jobs**

- **jobs**: Defines all the jobs within the workflow.
  - **<job_id>**: An identifier for a specific job.
    - **name**: The name of the job.
    - **runs-on**: Specifies the environment for the job (e.g., `ubuntu-latest`).
    - **steps**: Lists the steps in the job.
      - **uses**: Specifies a GitHub Action from the marketplace.
      - **run**: Executes a shell command.
      - **with**: Provides inputs for the action.
      - **env**: Sets environment variables for the step.

### 3. **Steps**

- **steps**: Contains the individual tasks to run in the job.
  - **name**: The name of the step.
  - **id**: A unique identifier for the step.
  - **uses**: References a GitHub Action.
  - **run**: Executes a shell command.
  - **with**: Specifies input parameters.
  - **env**: Sets environment variables.

### 4. **Matrix Strategy**

- **strategy**: Defines the strategy for the job.
  - **matrix**: Allows configuration of multiple environments for the job (e.g., different Node.js versions).

### 5. **Secrets**

- **secrets**: Provides access to sensitive data stored in GitHub Actions secrets.

### 6. **Environment Variables**

- **env**: Defines environment variables available to all jobs and steps in the workflow.

### 7. **Artifacts**

- **artifact**: Stores and retrieves files between jobs in the workflow.

### 8. **Caching**

- **cache**: Speeds up workflow execution by caching dependencies.

### 9. **Notifications**

- **notifications**: Configures alerts, such as email notifications, for workflow outcomes.

## Example CI Workflow

```yaml
name: CI Workflow # Defines the name of the workflow

on:
  push: # This workflow is triggered on push events to the repository
    branches:
      - main # Only triggers on pushes to the 'main' branch

jobs:
  build: # Job identifier: 'build'
    name: Build and Test Application # The name of the job
    runs-on: ubuntu-latest # Specifies the machine (ubuntu-latest) to run the job

    strategy:
      matrix:
        node-version: [14, 16] # Runs the job with Node.js versions 14 and 16

    steps: # Defines the individual steps within the job
      - name: Checkout code # Step 1: Checkout code from the repository
        uses: actions/checkout@v2 # Uses the actions/checkout GitHub action to clone the repository

      - name: Set up Node.js # Step 2: Set up Node.js environment
        uses: actions/setup-node@v2 # Uses the actions/setup-node GitHub action to install Node.js
        with:
          node-version: ${{ matrix.node-version }} # Uses the Node.js versions defined in the matrix (14, 16)

      - name: Install dependencies # Step 3: Install project dependencies
        run: npm install # Executes the shell command 'npm install' to install dependencies
        env:
          CI: true # Sets the 'CI' environment variable to true

      - name: Run tests # Step 4: Run tests
        run: npm test # Executes 'npm test' to run the test suite

      - name: Cache dependencies # Step 5: Cache Node.js dependencies
        uses: actions/cache@v2 # Uses the actions/cache GitHub action to cache dependencies
        with:
          path: ~/.npm # Specifies the path to the cache (Node.js modules in this case)
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }} # Defines the cache key based on OS and lock file hash
          restore-keys: |
            ${{ runner.os }}-node-  # Restore cache using the OS-based prefix if the primary key is not found

      - name: Upload artifact # Step 6: Upload build artifacts
        uses: actions/upload-artifact@v2 # Uses the actions/upload-artifact GitHub action to upload artifacts
        with:
          name: build-artifact # Specifies the name of the artifact
          path: ./path/to/artifact # Specifies the path to the artifact to upload

    secrets: # Define secrets used in this workflow
      - name: Secret Key # Secret name
        value: ${{ secrets.MY_SECRET }} # Accesses a secret stored in GitHub Actions secrets

    notifications: # Email notifications configuration
      email:
        on_failure: true # Only sends notifications if the job fails
        recipients:
          - dev@example.com # Sends the email to the specified recipient
```

# Explanation of the Workflow

### 1. **Workflow Name**: `CI Workflow`

- The workflow is named "CI Workflow" for easy identification.

### 2. **Trigger**: `on: [push]`

- This workflow is triggered by a `push` event to the `main` branch of the repository.

### 3. **Job Definition**: `build`

- The job `build` runs on an `ubuntu-latest` virtual machine.

### 4. **Matrix Strategy**

- The job is executed for two configurations (Node.js versions 14 and 16) defined in the matrix.

### 5. **Steps in the Job**:

- **Checkout Code**: Uses the `actions/checkout@v2` action to retrieve the repository's code.
- **Set up Node.js**: Configures the Node.js version based on the matrix configuration.
- **Install Dependencies**: Installs project dependencies using `npm install` with the `CI` environment variable set to `true`.
- **Run Tests**: Executes tests with `npm test`.
- **Cache Dependencies**: Caches the Node.js dependencies using the `actions/cache@v2` action to improve subsequent workflow speed.
- **Upload Artifact**: Uploads an artifact (e.g., build output) to be used later in the workflow.

### 6. **Secrets**:

- The workflow accesses a secret stored in GitHub Actions secrets (`MY_SECRET`) for sensitive data such as API keys.

### 7. **Notifications**:

- Configures email notifications to notify the recipient (`dev@example.com`) in case the job fails.

```

```

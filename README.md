# Vulnerable Node Express

This is a vulnerable Node Express service meant to be used as a target for security testing tools.

## Build and Run

### Install NPM Dependencies
```shell script
npm install
```

### Initialize SQLite DB
```shell
node bootstrapdb.js
```

### Run
```shell script
DEBUG=myapp:* npm start
```

## Build and Run with Docker

### Build Docker Image
```shell script
docker build --tag stackhawk/nodeexpressvulny .
```

### Run Docker Container
```shell script
docker run --rm --publish 3000:3000 --name nodeexpressvulny stackhawk/nodeexpressvulny
```

### Build and Run in Docker Compose
```shell script
docker-compose up --build --detach
```

## Known Vulnerabilities
* SQL Injection via search box. - `item%' union all select * from user; -- ` 
* Cross Site Scripting via search box. - `<script>alert("hey guy");</script>`

# Workshop Guidebook: Automating Application Security Testing in GitHub Actions

Hello everyone, and welcome to our presentation on automating application security testing in GitHub Actions. In today's talk, we will discuss the importance of security in software development, the challenges of manual security testing, and how GitHub Actions can help us automate this process. By the end of this presentation, you will have a better understanding of how to use GitHub Actions to ensure the security of your applications. So let's get started!

This workshop is designed to help you get started with application security testing in GitHub Actions. Participants get hands-on experience with:

* GitHub Actions workflows
* Dependabot software composition analysis (SCA)
* CodeQL static application security testing (SAST) scanning
* ZAP dynamic application security test (DAST) scanning


## Prerequisites

* GitHub - [Sign up](https://github.com/signup) if you don't have an account

## Step 1: Continuous Integration Workflows in GitHub Actions

Fork this repo: https://github.com/kaakaww/vuln_node_express

Go to the **Code** section of your newly forked repository in GitHub. Create a new file using the **Add file --> Create new file** button. Name the file `.github/workflows/build-and-test.yml`, and add the following contents:

```yaml
# .github/workflows/build-and-test.yml
name: Build and Test
on:
  push: 
    branches:
      - main
  pull_request:
jobs:
  build-and-test:
    name: Build and test
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Install Node.js 14.x
        uses: actions/setup-node@v3
        with:
          node-version: 14
          cache: npm
      - name: Install dependencies
        run: npm install
      - name: Run unit tests
        run: npm test
```

Commit the change.

Go to the **Actions** section of your repository, and you should see the new workflow running.

## Step 2: Dependency Scanning with Dependabot

Go to the **Settings** section of your repo, and find the **Code security & analysis** section in the left pane. 

Enable the **Dependency graph**, **Dependabot alerts**, and **Dependabot security updates** features in this section.

Dependabot is now configured.

Go to the **Security** section of your GitHub repo, and click into the **Dependabot alerts** on the left pane. Examine some of the dependency alerts, and see if you can resolve them.

## Step 3: Static Code Analysis with CodeQL

Go to the **Security** section of your repo. Click on **Set up code scanning**. Click the big green button to **Configure CodeQL alerts**.

Examine the GitHub Actions workflow, `.github/workflows/codeql-analysis.yml`, and commit it to the repo.

Now go to the **Actions** section of your repo, and watch your new CodeQL workflow run.

When CodeQL has finished, examine the results in the **Security** section under **Code scanning alerts** in the left pane.

## Step 4: Dynamic App Scanning with OWASP ZAP


Go to Settings>Issues > Enable Issues

### Add ZAP Scan to your Build and Test Workflow


```yaml
# .github/workflows/zap-scan.yml
name: ZAP-Test

on:
  push: 
    branches: [ main ]

jobs: 
  build: 
    runs-on: ubuntu-18.04
    steps: 
      - uses: actions/checkout@v2
      - name: docker compose
        run: docker-compose up --build --detach
      - name: ZAP Scan
        uses: zaproxy/action-full-scan@v0.4.0
        with:
          target: 'http://localhost:3000/'

```

Commit this change.

### Check your Scan Results

Go to the **Issues** section of your repo.


## Attacks

## VULN-1

https://github.com/williamhanugra/Vuln-1

```
name: Say Hi

on: [pull_request]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Say Hi
      run: |
        echo "Issue title: ${{ github.event.pull_request.title }}"
      env:
          FLAG: ${{ secrets.FLAG }}
```

## VULN-2

https://github.com/williamhanugra/Vuln-2

## Try your self
```
name: Nuclei - Vulnerability Scan
on: [pull_request]

jobs:
  nuclei-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Nuclei - Vulnerability Scan
        uses: projectdiscovery/nuclei-action@main
        with:
          target: https://www.bosch.co.id/
      - name: GitHub Workflow artifacts
        uses: actions/upload-artifact@v2
        with:
          name: nuclei.log
          path: nuclei.log
      - name: GitHub Security Dashboard Alerts update
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: nuclei.sarif
```
## Workshop Complete

You just automated SCA, SAST, and DAST scanning with GitHub Actions!

Reference: 
- https://github.com/kaakaww/workshop-github-actions
- https://www.youtube.com/watch?v=TI7E14vYWtU
- https://securitylab.github.com/research/github-actions-preventing-pwn-requests/

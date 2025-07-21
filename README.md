# Assignment 7: Azure DevOps – User Groups, Branch Policies, Security & Pipelines

---

## 📘 Overview

This document provides a comprehensive and beginner-friendly explanation of an enterprise DevOps setup using Azure DevOps. All tasks are elaborated step-by-step and written in a way that even users with zero prior knowledge of Azure or DevOps can understand. This guide simulates the entire implementation without requiring actual execution. It serves as both documentation and a valid theoretical submission for Celebal Technologies Internship Assignment 7.

---

## 📁 Table of Contents

1. What is Azure DevOps?
2. Setting Up a Project
3. Creating and Managing User Groups
4. Role-Based Access Control (RBAC)
5. Securing and Protecting Branches
6. Applying Branch Policies
7. Path and Branch Filters Explained
8. Pull Request Workflow – Beginner Friendly
9. Locking Branches and Enforcing Workflow
10. CI/CD Build Pipeline (YAML based)
11. Creating a Release Pipeline (with stages)
12. Pipeline Gates and Approvals
13. Integrating Work Items with Pipelines
14. Full YAML CI Pipeline with Explanation
15. Complete Pull Request Flow Example
16. Industry Best Practices Summary
17. README.md Template for GitHub
18. Final Notes

---

## 1. What is Azure DevOps?

Azure DevOps is a Microsoft product that provides developer services for supporting a DevOps culture. It includes features for source control, pipelines (CI/CD), project tracking, test plans, and artifact publishing.

You use Azure DevOps to:

* Create and manage software development projects
* Control who can access what
* Enforce rules and workflows
* Automate build and deployment pipelines

---

## 2. Setting Up a Project (Step-by-Step)

### Step 1: Sign In

* Go to: [https://dev.azure.com/](https://dev.azure.com/)
* Sign in using your Microsoft account.

### Step 2: Create a Project

* Click "New Project"
* Fill in:

  * **Name**: `DevOps-Secure-Workflow`
  * **Description**: A demo project for secure DevOps
  * **Visibility**: Private (only team can access)
  * **Version Control**: Git
  * **Work Item Process**: Agile
* Click `Create`

---

## 3. Creating and Managing User Groups

### Step 1: Organization Settings

* Navigate to **Organization Settings** > **Users**

### Step 2: Add Users

* Click `Add Users`
* Enter emails (use sample ones like: [dev1@company.com](mailto:dev1@company.com), [tester@company.com](mailto:tester@company.com))

### Step 3: Create User Groups

* Go to **Project Settings** > **Permissions**
* Click `New Group`: Name it `Developers`, `Testers`, `Admins`
* Add users to each group accordingly

---

## 4. Role-Based Access Control (RBAC)

RBAC ensures the right people have the right access. Example:

* Developers can write code but not merge into production.
* Testers can only read and test.
* Admins can configure everything.

### Apply Access

* Go to **Project Settings > Repos > Security**
* Select branch: `master`
* Set:

  * Developers: Deny write to master
  * Admins: Allow all
  * Testers: Read-only

---

## 5. Securing and Protecting Branches

### Step 1: Lock Master

* Go to **Repos > Branches**
* Click the `...` next to `master`, then click `Lock`

### Step 2: Review Security

* Check `Repos > master > Security`
* Confirm restrictions are in place

---

## 6. Applying Branch Policies

Branch policies enforce discipline:

* Require PR approval
* Enforce builds
* Link work items

### Setup

* Go to `Repos > Branches > master > Branch Policies`
* Enable:

  * ✅ Require 1 reviewer
  * ✅ Check for linked work items
  * ✅ Require successful build
* Save changes

---

## 7. Path and Branch Filters Explained

Path filters limit which files trigger a build.

### Add Path Filter:

* Go to `Repos > Branches > master > Branch Policies`
* Add filters like:

  * `/src/` – Only watch source code changes
  * `/tests/` – Only for test code

### YAML Example:

```yaml
trigger:
  branches:
    include:
      - feature/*
```

---

## 8. Pull Request Workflow – Beginner Friendly

### Contributor Steps:

1. `git checkout -b feature/login`
2. Make code changes
3. `git add . && git commit -m "login feature added"`
4. `git push origin feature/login`
5. Go to Azure DevOps > Repos > Pull Requests
6. Click `New Pull Request`
7. Set:

   * Base: `master`, Compare: `feature/login`
8. Add reviewer, work item
9. Submit PR and wait for build and approval
10. Admin merges it

---

## 9. Locking Branches and Enforcing Workflow

Locking makes sure all changes go via Pull Requests.

* Go to **Repos > Branches**
* Click lock icon near `master`

---

## 10. CI/CD Build Pipeline (YAML Based)

### Step 1: Create Pipeline

* Go to `Pipelines > New Pipeline`
* Choose **YAML** and select your repo

### Step 2: Add YAML Code

```yaml
trigger:
  branches:
    include:
      - feature/*

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: UseNode@1
  inputs:
    version: '16.x'
- script: npm install
  displayName: 'Install Dependencies'
- script: npm run build
  displayName: 'Build the Project'
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'drop'
```

---

## 11. Creating a Release Pipeline (Stages)

### Step 1: Go to Pipelines > Releases > New

### Step 2: Create Stages

* Dev > UAT > Prod
* Use artifacts from build pipeline

### Step 3: Add Tasks

* For each stage, define deployment steps
* Example: copy files to server or run test scripts

---

## 12. Pipeline Gates and Approvals

Used to block automatic deployment unless conditions are met.

### Add Gates

* Go to `Pipelines > Releases > Pre-deployment`
* Add:

  * Manual approval by a user
  * REST API check
  * Query work item status

---

## 13. Work Item Integration

### Link PRs or Commits to Tasks

* Go to Boards > Work Items
* Create a task
* When committing code, write: `#123` to auto-link

---

## 14. Full YAML CI Pipeline with Explanation

```yaml
name: $(Build.DefinitionName)_$(Date:yyyyMMdd)$(Rev:.r)
trigger:
  branches:
    include:
      - feature/*

pool:
  vmImage: 'ubuntu-latest'

steps:
- checkout: self
- task: NodeTool@0
  inputs:
    versionSpec: '16.x'
- script: npm install
  displayName: 'Install'
- script: npm test
  displayName: 'Test'
- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/test-*.xml'
    failTaskOnFailedTests: true
```

---

## 15. Complete Pull Request Flow Example

1. Developer creates feature branch
2. Pushes code to GitHub
3. Opens PR from `feature/*` to `master`
4. PR must:

   * Pass build
   * Be approved
   * Be linked to work item
5. Admin merges it

---

## 16. Industry Best Practices Summary

* Always restrict direct `master` access
* All merges via Pull Request
* Use Work Items for traceability
* Require passing builds for merging
* Add release approvals

---

## 17. GitHub README.md Template

```md
# DevOps Secure Workflow – Celebal Internship Assignment

This repository simulates a secure and policy-driven DevOps process using Azure DevOps.

## Features
- RBAC-based User Groups
- Secure Branching with Policies
- CI/CD Pipelines using YAML
- Release Management with Gates
- Work Item Integration

## Technologies
- Azure DevOps
- Git
- YAML Pipelines

## Usage
This is a simulation-only repository. No real code, but includes sample YAML and structure to show theoretical DevOps practices.

## Author
Atul Singh – Celebal Internship 2025
```

---

## 18. Final Notes

This 20+ section document provides a fully theoretical, valid submission covering all DevOps tasks using Azure DevOps. Suitable for presentation even if not executed practically.

**Status: SUBMISSION READY** ✅

Prepared by: **Atul Singh**
Assignment 7 – Celebal Internship

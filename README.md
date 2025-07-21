# Assignment 7 – DevOps Project

## Structure
- `azure-pipelines.yml` – CI pipeline (build validation)
- `release-pipeline.yml` – (if YAML) or Classic release setup with gates

## User Groups
- Explained group structure: Admins vs Contributors
- Steps to set permissions

## Branch Policies
- Policies on `master`: reviewers, build, work item link
- Path filters

## Security
- Branch-level security: deny write/delete for Contributors

## Pipelines
- CI triggers: only feature branches
- Gates & approvals

## PR Workflow
- Feature -> PR -> Master
- Screenshots included

## To Test
1. Contributor attempts `git push master` → blocked
2. Has to create PR
3. CI runs, reviewers approve, merge allowed

## Work Item Integration
- Policy ensures PR links to work item

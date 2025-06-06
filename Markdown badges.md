# Comprehensive Markdown Badge Collection

[![hackmd-github-sync-badge](https://hackmd.io/5wgPxx1OTfaVDYUVeofRpQ/badge)](https://hackmd.io/5wgPxx1OTfaVDYUVeofRpQ)

This document provides a comprehensive collection of markdown badge codes for various platforms and services, with a special focus on GitHub badges.

## Table of Contents

- [GitHub Badges](#github-badges)
  - [Repository Status](#repository-status)
  - [GitHub Actions Workflow](#github-actions-workflow)
  - [GitHub Codespaces](#github-codespaces)
  - [Social & Stats](#social--stats)
  - [Dependency Status](#dependency-status)
- [Package Managers](#package-managers)
- [Version Control](#version-control)
- [CI/CD](#cicd)
- [Documentation](#documentation)
- [Code Quality](#code-quality)
- [License](#license)
- [Dynamic & Custom Badges](#dynamic--custom-badges)
- [Resources](#resources)

## GitHub Badges

### Repository Status

```markdown
<!-- Stars -->
![GitHub stars](https://img.shields.io/github/stars/username/repo)

<!-- Forks -->
![GitHub forks](https://img.shields.io/github/forks/username/repo)

<!-- Watchers -->
![GitHub watchers](https://img.shields.io/github/watchers/username/repo)

<!-- Issues -->
![GitHub issues](https://img.shields.io/github/issues/username/repo)

<!-- Open Issues -->
![GitHub open issues](https://img.shields.io/github/issues-raw/username/repo)

<!-- Closed Issues -->
![GitHub closed issues](https://img.shields.io/github/issues-closed/username/repo)

<!-- Pull Requests -->
![GitHub pull requests](https://img.shields.io/github/issues-pr/username/repo)

<!-- Closed Pull Requests -->
![GitHub closed pull requests](https://img.shields.io/github/issues-pr-closed/username/repo)

<!-- Repo Size -->
![GitHub repo size](https://img.shields.io/github/repo-size/username/repo)

<!-- Code Size -->
![GitHub code size in bytes](https://img.shields.io/github/languages/code-size/username/repo)

<!-- Last Commit -->
![GitHub last commit](https://img.shields.io/github/last-commit/username/repo)

<!-- Release Date -->
![GitHub release date](https://img.shields.io/github/release-date/username/repo)

<!-- Latest Release Version -->
![GitHub release (latest by date)](https://img.shields.io/github/v/release/username/repo)

<!-- Contributors -->
![GitHub contributors](https://img.shields.io/github/contributors/username/repo)

<!-- Commit Activity -->
![GitHub commit activity](https://img.shields.io/github/commit-activity/m/username/repo)
```

### GitHub Actions Workflow

```markdown
<!-- GitHub Actions Workflow Status -->
![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/username/repo/workflow.yml)

<!-- GitHub Actions Workflow Status (branch) -->
![GitHub Workflow Status (branch)](https://img.shields.io/github/actions/workflow/status/username/repo/workflow.yml?branch=main)

<!-- Official GitHub Actions badge (SVG from repo) -->
![example workflow](https://github.com/username/repo/actions/workflows/workflow.yml/badge.svg)

<!-- Official GitHub Actions badge (specific branch) -->
![example workflow](https://github.com/username/repo/actions/workflows/workflow.yml/badge.svg?branch=main)

<!-- Official GitHub Actions badge (specific event) -->
![example workflow](https://github.com/username/repo/actions/workflows/workflow.yml/badge.svg?event=push)
```

### GitHub Codespaces

```markdown
<!-- Open in GitHub Codespaces -->
[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/username/repo)

<!-- Open in GitHub Codespaces (specific branch) -->
[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/username/repo/tree/branch-name)

<!-- Open with Remote Repositories -->
[![Open with Remote Repositories](https://img.shields.io/static/v1?label=Remote%20repositories&message=Open&color=brightgreen&logo=visualstudiocode)](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-repositories/cloneInNewWindow?url=https://github.com/username/repo)
```

### Social & Stats

```markdown
<!-- GitHub Followers -->
![GitHub followers](https://img.shields.io/github/followers/username?style=social)

<!-- GitHub User's stars -->
![GitHub User's stars](https://img.shields.io/github/stars/username?style=social)

<!-- GitHub Sponsors -->
![GitHub Sponsors](https://img.shields.io/github/sponsors/username)

<!-- GitHub repository topics -->
![GitHub repo topics](https://img.shields.io/github/topics/username/repo)

<!-- GitHub language count -->
![GitHub language count](https://img.shields.io/github/languages/count/username/repo)

<!-- GitHub top language -->
![GitHub top language](https://img.shields.io/github/languages/top/username/repo)
```

### Dependency Status

```markdown
<!-- Dependencies -->
![Depfu](https://img.shields.io/depfu/dependencies/github/username/repo)

<!-- Depfu -->
![Depfu](https://badges.depfu.com/badges/example/overview.svg)

<!-- Snyk Vulnerabilities -->
![Snyk Vulnerabilities for GitHub Repo](https://img.shields.io/snyk/vulnerabilities/github/username/repo)
```

## Package Managers

### npm

```markdown
<!-- npm version -->
![npm](https://img.shields.io/npm/v/package-name)

<!-- npm downloads -->
![npm](https://img.shields.io/npm/dm/package-name)

<!-- npm bundle size -->
![npm bundle size](https://img.shields.io/bundlephobia/min/package-name)

<!-- npm bundle size (minified + gzip) -->
![npm bundle size](https://img.shields.io/bundlephobia/minzip/package-name)
```

### PyPI

```markdown
<!-- PyPI version -->
![PyPI](https://img.shields.io/pypi/v/package-name)

<!-- PyPI downloads -->
![PyPI - Downloads](https://img.shields.io/pypi/dm/package-name)

<!-- PyPI format -->
![PyPI - Format](https://img.shields.io/pypi/format/package-name)

<!-- PyPI Python versions -->
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/package-name)
```

### Docker

```markdown
<!-- Docker Image Version -->
![Docker Image Version (latest by date)](https://img.shields.io/docker/v/username/repo)

<!-- Docker Pulls -->
![Docker Pulls](https://img.shields.io/docker/pulls/username/repo)

<!-- Docker Image Size -->
![Docker Image Size (latest by date)](https://img.shields.io/docker/image-size/username/repo)
```

### Others

```markdown
<!-- Crates.io -->
![Crates.io](https://img.shields.io/crates/v/crate-name)

<!-- NuGet -->
![Nuget](https://img.shields.io/nuget/v/package-name)

<!-- Packagist -->
![Packagist Version](https://img.shields.io/packagist/v/vendor/package)

<!-- Gem -->
![Gem](https://img.shields.io/gem/v/gem-name)

<!-- Maven Central -->
![Maven Central](https://img.shields.io/maven-central/v/group-id/artifact-id)
```

## CI/CD

```markdown
<!-- Travis CI -->
[![Build Status](https://travis-ci.org/username/repo.svg?branch=master)](https://travis-ci.org/username/repo)

<!-- CircleCI -->
[![CircleCI](https://circleci.com/gh/username/repo.svg?style=svg)](https://circleci.com/gh/username/repo)

<!-- Jenkins -->
[![Build Status](https://jenkins.example.com/buildStatus/icon?job=project-name)](https://jenkins.example.com/job/project-name/)

<!-- GitLab CI -->
[![pipeline status](https://gitlab.com/username/repo/badges/master/pipeline.svg)](https://gitlab.com/username/repo/-/commits/master)

<!-- AppVeyor -->
[![Build status](https://ci.appveyor.com/api/projects/status/project-id?svg=true)](https://ci.appveyor.com/project/username/repo)

<!-- Azure DevOps builds -->
[![Build Status](https://dev.azure.com/organization/project/_apis/build/status/repo?branchName=master)](https://dev.azure.com/organization/project/_build/latest?definitionId=id&branchName=master)
```

## Documentation

```markdown
<!-- Read the Docs -->
[![Documentation Status](https://readthedocs.org/projects/project-name/badge/?version=latest)](https://project-name.readthedocs.io/en/latest/?badge=latest)

<!-- Documentation -->
[![Documentation](https://img.shields.io/badge/documentation-yes-brightgreen.svg)](https://github.com/username/repo/wiki)
```

## Code Quality

```markdown
<!-- Code Climate maintainability -->
[![Maintainability](https://api.codeclimate.com/v1/badges/repo-id/maintainability)](https://codeclimate.com/github/username/repo/maintainability)

<!-- Code Climate test coverage -->
[![Test Coverage](https://api.codeclimate.com/v1/badges/repo-id/test_coverage)](https://codeclimate.com/github/username/repo/test_coverage)

<!-- Codecov -->
[![codecov](https://codecov.io/gh/username/repo/branch/master/graph/badge.svg)](https://codecov.io/gh/username/repo)

<!-- Coveralls -->
[![Coverage Status](https://coveralls.io/repos/github/username/repo/badge.svg?branch=master)](https://coveralls.io/github/username/repo?branch=master)

<!-- CodeFactor -->
[![CodeFactor](https://www.codefactor.io/repository/github/username/repo/badge)](https://www.codefactor.io/repository/github/username/repo)

<!-- SonarCloud -->
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=project-key&metric=alert_status)](https://sonarcloud.io/dashboard?id=project-key)
```

## License

```markdown
<!-- GitHub -->
![GitHub](https://img.shields.io/github/license/username/repo)

<!-- REUSE compliance -->
[![REUSE status](https://api.reuse.software/badge/github.com/username/repo)](https://api.reuse.software/info/github.com/username/repo)
```

## Dynamic & Custom Badges

```markdown
<!-- Custom badge (shields.io) -->
![Custom badge](https://img.shields.io/badge/label-message-color)

<!-- Custom badge with logo -->
![Custom badge](https://img.shields.io/badge/label-message-color?logo=logoname&logoColor=logocolor)

<!-- Endpoint badge -->
![Custom badge](https://img.shields.io/endpoint?url=https://example.com/badge.json)
```

## Resources

- [Shields.io](https://shields.io/) - The primary source for most badges
- [GitHub Actions Badges](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge)
- [GitHub Codespaces Badge](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/setting-up-your-repository-for-codespaces)
- [Awesome Badges](https://github.com/badges/awesome-badges) - A curated list of badges
- [Badge Examples](https://github.com/alexandresanlim/Badges4-README.md-Profile) - Collection of badges for GitHub profile READMEs
- [For the Badge](https://forthebadge.com/) - Fun badge styles

---

*This document was created as a comprehensive reference for markdown badges. Feel free to contribute by adding more badge examples!*
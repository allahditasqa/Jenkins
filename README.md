# Jenkins

**Automation Framework:** Declarative Jenkins pipeline that runs my [Playwright](https://github.com/allahditasqa/Playwright) and [Cypress](https://github.com/allahditasqa/Cypress) suites in Docker agents and compile-checks the [Appium](https://github.com/allahditasqa/Appium) project, plus a Dockerfile/Compose to start a local Jenkins.

## What the pipeline does
| Stage | Docker agent | Action |
|-------|--------------|--------|
| Playwright (UI + API) | `node:22-bookworm` | clone repo, `npm ci`, install Chromium, `npm test`, archive reports |
| Cypress (UI + API) | `cypress/browsers:latest` | clone repo, `npm ci`, `npm test`, archive reports |
| Appium (compile only) | `maven:3.9-eclipse-temurin-17` | `mvn test-compile` - device tests need an emulator/real device, so they are **not** run in this pipeline |

Parameters: `SUITE` (`all`, `playwright`, `cypress`, `appium-compile`) and `BRANCH`. Targets are public demo services only; no credentials are used.

## Structure
```
Jenkins/
├── Jenkinsfile               # declarative pipeline
├── docker-compose.yml        # local Jenkins on :8080
└── jenkins/
    ├── Dockerfile            # jenkins lts + docker CLI
    └── plugins.txt           # pipeline, docker-workflow, git, timestamper
```

## Try it locally
1. `docker compose up -d --build` then open http://localhost:8080 and finish the setup wizard (initial password: `docker compose exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword`).
2. New Item → **Pipeline** → *Pipeline script from SCM* → this repository (`Jenkinsfile`), or paste the file into *Pipeline script*.
3. **Build with Parameters**.

The `Jenkinsfile` can also be used on any existing Jenkins that has Docker on its agents and the *Docker Pipeline* plugin.

## Status / honesty note
The Jenkinsfile and Docker setup were written against the public repos above, but were **not executed on a Jenkins server** while creating this repo (no Jenkins/Docker on the authoring machine). The commands in each stage are the same ones the Playwright and Cypress repos use in their own GitHub Actions workflows.

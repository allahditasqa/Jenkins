# Pipeline documentation

No test cases are documented in this repository on purpose: Jenkins only **runs** the suites of other repositories. Their test cases are documented there (Playwright, Cypress; Appium is compile-only).

## Stages
| Stage | Agent | What it checks | Result you see |
| --- | --- | --- | --- |
| Playwright (UI + API) | `node:22-bookworm` | the Playwright suite passes | stage status, archived `reports/` of that repo |
| Cypress (UI + API) | `cypress/browsers:latest` | the Cypress suite passes | stage status, archived mochawesome reports |
| Appium (compile only) | `maven:3.9-eclipse-temurin-17` | the Appium project compiles | stage status |

## Parameters
`SUITE` (`all`, `playwright`, `cypress`, `appium-compile`) and `BRANCH`.

## Status
Verified: the Compose file is valid and the Jenkins image builds (GitHub Actions of this repo). **Not verified:** the `Jenkinsfile` has never run on a Jenkins server.

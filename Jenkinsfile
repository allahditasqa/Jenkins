// Declarative pipeline: runs the public Playwright, Cypress and Appium (compile-only) projects
// in Docker agents. Requires a Jenkins with Docker access (see docker-compose.yml in this repo)
// and the "Docker Pipeline" plugin.
pipeline {
  agent none

  options {
    timestamps()
    timeout(time: 45, unit: 'MINUTES')
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }

  parameters {
    choice(name: 'SUITE', choices: ['all', 'playwright', 'cypress', 'appium-compile'], description: 'What to run')
    string(name: 'BRANCH', defaultValue: 'main', description: 'Branch of the suite repositories')
  }

  environment {
    GH_BASE = 'https://github.com/allahditasqa'
    CI = 'true'
  }

  stages {
    stage('Playwright (UI + API)') {
      when { expression { params.SUITE in ['all', 'playwright'] } }
      agent { docker { image 'node:22-bookworm'; args '--ipc=host' } }
      steps {
        dir('playwright') {
          git url: "${GH_BASE}/Playwright.git", branch: params.BRANCH
          sh 'npm ci'
          sh 'npx playwright install --with-deps chromium'
          sh 'npm test'
        }
      }
      post {
        always {
          archiveArtifacts artifacts: 'playwright/reports/**, playwright/playwright-report/**', allowEmptyArchive: true
        }
      }
    }

    stage('Cypress (UI + API)') {
      when { expression { params.SUITE in ['all', 'cypress'] } }
      agent { docker { image 'cypress/browsers:latest'; args '--entrypoint=' } }
      steps {
        dir('cypress') {
          git url: "${GH_BASE}/Cypress.git", branch: params.BRANCH
          sh 'npm ci'
          sh 'npm test'
        }
      }
      post {
        always {
          archiveArtifacts artifacts: 'cypress/mochawesome-report/**, cypress/reports/**', allowEmptyArchive: true
        }
      }
    }

    stage('Appium (compile only)') {
      when { expression { params.SUITE in ['all', 'appium-compile'] } }
      agent { docker { image 'maven:3.9-eclipse-temurin-17' } }
      steps {
        dir('appium') {
          git url: "${GH_BASE}/Appium.git", branch: params.BRANCH
          // Device tests need an emulator/real device, so CI only proves the project compiles.
          sh 'mvn -B -q -DskipTests test-compile'
        }
      }
    }
  }

  post {
    success { echo 'Pipeline finished successfully' }
    failure { echo 'Pipeline failed - check the stage logs and archived reports' }
  }
}

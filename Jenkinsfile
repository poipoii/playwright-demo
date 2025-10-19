pipeline {
  agent any

  tools {
    nodejs 'NodeJS 18'
  }

  environment {
    PLAYWRIGHT_BROWSERS_PATH = "${WORKSPACE}/.cache/ms-playwright"
  }

  stages {
    stage('Install dependencies') {
      steps {
        sh 'npm ci'
      }
    }

    stage('Install Playwright browsers') {
      steps {
        // No --with-deps because Jenkins user is not root
        sh 'npx playwright install'
      }
    }

    stage('Run Playwright tests') {
      steps {
        sh 'npx playwright test --reporter=line'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'playwright-report/**', allowEmptyArchive: true
    }
  }
}

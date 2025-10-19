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
        sh 'npx playwright install-deps --dry-run'
        // No --with-deps because Jenkins user is not root
        sh 'npx playwright install'
      }
    }

    stage('Run Playwright tests') {
      steps {
        sh 'npx playwright test --reporter=line,html'
      }
    }
  }

  post {
    always {
      // https://www.jenkins.io/doc/book/security/configuring-content-security-policy
      // sudo nano /usr/lib/systemd/system/jenkins.service
      // Environment="JAVA_OPTS=-Djava.awt.headless=true -Dhudson.model.DirectoryBrowserSupport.CSP=\"sandbox allow-same-origin allow-scripts allow-popups allow-downloads; default-src 'self'; img-src 'self' data:; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-inline'; media-src 'self'; font-src 'self'; frame-src 'self' data:;\""
      // sudo systemctl daemon-reload
      // sudo service jenkins restart
      archiveArtifacts artifacts: 'playwright-report/**', allowEmptyArchive: true
      publishHTML(target: [
        allowMissing: false,
        alwaysLinkToLastBuild: true,
        keepAll: true,
        reportDir: 'playwright-report',
        reportFiles: 'index.html',
        reportName: 'Playwright Test Report'
      ])
    }
  }
}

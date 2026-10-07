pipeline {
  agent any
  tools { maven 'maven-3'; jdk 'jdk11' }
  stages {
    stage('Build & Test') {
      steps { sh 'mvn -B clean verify' }
      post { always { junit '**/target/surefire-reports/*.xml' } }
    }
    stage('SonarQube') {
      steps { withSonarQubeEnv('sonar') { sh 'mvn -B sonar:sonar -Dsonar.coverage.jacoco.xmlReportPaths=core/target/site/jacoco/jacoco.xml' } }
    }
    stage('Quality Gate') {
      steps { timeout(time: 10, unit: 'MINUTES') { waitForQualityGate abortPipeline: true } }
    }
    stage('Package') {
      when { branch 'main' }
      steps { archiveArtifacts artifacts: 'ui.apps/target/*.zip', fingerprint: true }
    }
    // Veracode: add the Veracode Jenkins plugin step here to scan ui.apps/target/*.zip
  }
}

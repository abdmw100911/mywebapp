pipeline {
  agent any
  tools {
    jdk 'JDK 17'         // the name you configured
    maven 'Maven3'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        bat 'mvn -B -DskipTests clean package'
      }
    }
    stage('Archive') {
      steps {
        archiveArtifacts artifacts: 'target/*.war', fingerprint: true
      }
    }
    stage('Deploy to Tomcat') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'tomcat-creds',
                                         usernameVariable: 'TOMCAT_USER',
                                         passwordVariable: 'TOMCAT_PASS')]) {
          bat """
            curl -u %TOMCAT_USER%:%TOMCAT_PASS% ^
              -T target\\mywebapp.war ^
              "http://localhost:7080/manager/text/deploy?path=/mywebapp&update=true"
          """
        }
      }
    }
  }
  post {
    success { echo '✅ Build & Deploy successful!' }
    failure { echo '❌ Build or Deploy failed.' }
  }
}

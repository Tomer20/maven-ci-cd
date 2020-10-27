pipeline {
  agent {
    label 'master'
  }
  stages {
    stage('mvn') {
      steps {
        dir("app") {
          script { 
            sh '''
              mkdir .mvn && mv wrapper .mvn/.
              ./mvnw clean install
              ./mvnw test
              ./mvnw package
            '''
          }
        }
      }
    }
    stage('docker build') {
      steps {
        script { 
          sh '''
            docker-compose build
          '''
        }
      }
    }
    stage('deploy') {
      steps {
        script { 
          sh '''
            docker-compose up -d
            sleep 3 && curl -s http://35.198.113.119:8080/ | jq || echo success
          '''
        }
      }
    }
    stage ("git_tag") {
      steps {
        script { 
          sh '''
            git tag Jenkins-${BUILD_NUMBER}
            git push origin --tags
          '''
        }
      }
    }
  }
  post {
    always {
      junit 'app/target/surefire-reports/*.xml'
    }
  }
}
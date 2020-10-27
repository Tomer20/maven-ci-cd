pipeline {
  agent {
    label 'master'
  }
  stages {
    stage('git checkout') {
      steps {
        git branch: "main", credentialsId: 'b34bd7bf-a44a-4f1c-b8cb-7d198afad3b1', url: "git@github.com:Tomer20/demo.git"
      }
    }
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
            curl http://35.198.113.119:8080/failll
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
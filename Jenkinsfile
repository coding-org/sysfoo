pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        echo 'compile maven app'
        sh 'mvn compile'
      }
    }

    stage('test') {
      parallel {
        stage('test') {
          steps {
            echo 'test maven app'
            sh 'mvn clean test'
          }
        }

        stage('integration-tests') {
          steps {
            echo 'i am integration tests'
            sleep 5
            script {
              if (fileExists('src/main/webapp/assets/textfile.txt')) {
                  echo "File found!"
              } else {
                  error 'Required file not found!'
              }
            }
            isUnix()
          }
        }

      }
    }

    stage('package') {
      steps {
        echo 'package maven app'
        sh 'mvn package -DskipTests'
        archiveArtifacts 'target/*.war'
      }
    }

  }
  tools {
    maven 'Maven 3.6.3'
  }
  options {
    timeout(time: 20, unit: 'SECONDS')
    retry(3)
  }
}

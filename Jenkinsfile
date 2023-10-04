pipeline {
  agent none
  stages {
    stage('build') {
      agent {
        docker {
          image 'maven:3.6.3-jdk-11-slim'
        }

      }
      steps {
        echo 'compile maven app'
        sh 'mvn compile'
      }
    }

    stage('test') {
      parallel {
        stage('unit-tests') {
          agent {
            docker {
              image 'maven:3.6.3-jdk-11-slim'
            }

          }
          steps {
            echo 'test maven app'
            sh 'mvn clean test'
          }
        }

        stage('integration-tests') {
          agent {
            docker {
              image 'maven:3.6.3-jdk-11-slim'
            }

          }
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
      when {
        branch 'master'
      }
      parallel {
        stage('package') {
          agent {
            docker {
              image 'maven:3.6.3-jdk-11-slim'
            }

          }
          steps {
            echo 'package maven app'
            sh 'mvn package -DskipTests'
            archiveArtifacts 'target/*.war'
          }
        }

        stage('build & package') {
          agent any
          steps {
            script {
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                def dockerImage = docker.build("guptakunal/sysfoo:v${env.BUILD_ID}", "./")
                dockerImage.push()
                dockerImage.push("latest")
                dockerImage.push("dev")
              }
            }

          }
        }

      }
    }

    stage('Deploy to Dev') {
      agent any
      steps {
        sh 'docker-compose up -d'
      }
    }

  }
  options {
    retry(2)
  }
}
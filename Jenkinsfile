pipeline {
  agent {
    node {
      label 'utest'
    }
    
  }
  stages {
    stage('Build') {
      agent {
        docker {
          reuseNode true
          image 'maven:3.5.0-jdk-8'
        }
        
      }
      steps {
        withMaven(options: [findbugsPublisher(), junitPublisher(ignoreAttachments: false)]) {
          sh 'mvn clean findbugs:findbugs package'
        }
        
      }
      post {
        success {
          archiveArtifacts(artifacts: '**/target/*.jar', allowEmptyArchive: true)
          
        }
        
      }
    }
    stage('Quality Analysis') {
      parallel {
        stage('Integration Test') {
          agent any
          steps {
            echo 'Run integration tests here...'
          }
        }
        stage('Sonar Scan') {
          agent {
            docker {
              reuseNode true
              image 'maven:3.5.0-jdk-8'
            }
            
          }
          environment {
            SONAR = credentials('sonar')
          }
          steps {
            sh 'mvn sonar:sonar -Dsonar.login=$SONAR_PSW'
          }
        }
      }
    }
    stage('Build and Publish Image') {
      when {
        branch 'master'
      }
      steps {
        sh """
                  docker build -t ${IMAGE} .
                  docker tag ${IMAGE} ${IMAGE}:${VERSION}
                  docker push ${IMAGE}:${VERSION}
                """
      }
    }
  }
  environment {
    IMAGE = readMavenPom().getArtifactId()
    VERSION = readMavenPom().getVersion()
  }
  post {
    failure {
      mail(to: 'team@example.com', subject: "Failed Pipeline: ${currentBuild.fullDisplayName}", body: "Something is wrong with ${env.BUILD_URL}")
      
    }
    
  }
  options {
    timestamps()
  }
}
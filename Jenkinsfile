pipeline {

    agent {
        node {
            label 'docker'
        }
    }

    stages {
        stage('Run') {
            agent {
                docker {
                    image 'hello-world'
                }
            }
        }
    }
}

pipeline {
    agent { docker { image 'python:3.5.1' } }
    stages {
        stage('build') {
            steps {
                sh 'python --version'
                sh 'echo "hello-world"'
            }
        }
    }
   agent { dockerfile true }
    stages {
        stage('dockerfile') {
            steps {
                sh 'python --version'
            }
        }
    }
}

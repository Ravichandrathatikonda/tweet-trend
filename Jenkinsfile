pipeline {
    agent {
        node {
            label 'maven'
        }       
    }

environment {
    PATH = "/opt/apache-maven-3.9.10/bin:$PATH"
}

# We have removed checkout stage as Jenkins is by default capturing that stage while running the job

    stages {
        stage('build') {
            steps {
                sh 'mvn clean deploy'
            }
        }
    }
}

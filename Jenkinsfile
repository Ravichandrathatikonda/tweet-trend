pipeline {
    agent {
        node {
            label 'maven'
        }       
    }

environment {
    PATH = "/opt/apache-maven-3.9.10/bin:$PATH"
}

//We have removed checkout stage as Jenkins is by default capturing that stage while running the job

    stages {
        stage('build') {
            steps {
                sh 'mvn clean deploy -DskipTests=true'
            }
        }
        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'valaxy-sonar-scanner'
            }
            steps{
                withSonarQubeEnv('valaxy-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=valaxy-project-2-key_twittertrend -Dsonar.organization=valaxy-project-2-key -Dsonar.sources=."
                }
            }   
        }
    }
}

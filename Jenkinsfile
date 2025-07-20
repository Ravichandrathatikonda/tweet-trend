pipeline {
    agent {
        label 'maven'
    }

    stages {
        stage('clone-code') {
            steps {
                git branch: 'main', url: 'https://github.com/Ravichandrathatikonda/tweet-trend.git'
            }
        }
    }
}

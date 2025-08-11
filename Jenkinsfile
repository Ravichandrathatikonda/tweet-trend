def registry = 'https://trialu23six.jfrog.io'
def imageName = 'trialu23six.jfrog.io/valaxy-docker-local/tweettrend'
def version   = '2.1.4'

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
        /* stage('test') {
            steps {
                echo "-----unit tests started------"
                sh 'mvn surefire-report:report'
                echo "------unit tests completed----"
            }
        } */
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

        //commented this section as sonarqube is not allowing to select new quality gate and asking for subscription
        /* stage("Quality Gate") {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                    def qg = waitForQualityGate() // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    if (qg.status != OK) {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
                
            }
        } */

        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "docker-trial/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
                }
            }   
        }   


        stage(" Docker Build ") {
            steps {
                script {
                    echo '<--------------- Docker Build Started --------------->'
                    app = docker.build(imageName+":"+version)
                    echo '<--------------- Docker Build Ends --------------->'
                }
            }
        }

        stage (" Docker Publish "){
            steps {
                script {
                    echo '<--------------- Docker Publish Started --------------->'  
                    docker.withRegistry(registry, 'jfrog-cred'){
                    app.push()
                    }       
                    echo '<--------------- Docker Publish Ended --------------->'  
                }
            }
        }
    }
}

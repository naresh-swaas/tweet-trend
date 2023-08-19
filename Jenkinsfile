pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.4/bin:$PATH"
}
    stages {
        stage("Build Stage"){
            steps {
                sh 'mvn clean deploy -Dmaven.test.skip=true'
            }
        }
        stage("Unit Test Stage"){
            steps {
                sh 'mvn surefire-report:report'
            }
        }        
        stage("SonarQube Analysis"){
            environment {
                scannerHome = tool "sonarqube-scanner"
            }
            steps {
                withSonarQubeEnv("sonarqube-server"){
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}

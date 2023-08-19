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
        stage("build"){
            steps {
                sh 'mvn clean deploy'
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

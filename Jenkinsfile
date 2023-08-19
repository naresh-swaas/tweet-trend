def registry = "https://tweettrend.jfrog.io/"
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

  stage("Quality Gate"){
    steps {
        script {
            timeout(time: 1, unit: 'HOURS') 
            { 
                def qg = waitForQualityGate()
                if (qg.status != 'OK') {
                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
                }
            }
                }
        }
    }

    stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
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


    }
}

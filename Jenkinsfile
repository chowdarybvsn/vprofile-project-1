pipeline {
	agent any
    environment{
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_VERSION = "nexus3"
        NEXUSIP = "10.0.1.23"
        NEXUSPORT= "8081"
        NEXUS_USER = "admin"
        NEXUS_PASS = "admin123"
        RELEASE_REPO = "vprofile-release"
        CENTRAL_REPO = "vpro-maven-central"
	    NEXUS_GRP_REPO = "vpro-maven-group"
        NEXUS_LOGIN = "nexuslogin"
        SONARSERVER = "mysonar"
        SONARSCANNER = "sonarscanner"
    }
    stages{
       stage("Build") {
         steps {
           sh 'mvn -s settings.xml -DskipTests install'
         }
         post{
            success{
                echo "archive artifacts"
                archiveArtifacts artifacts: '**/*.war'
            }
         } 
       }
       stage("Test"){
          steps{
             sh "mvn -s settings.xml test"  
          }
       } 
       stage("checkstyle"){
           steps{
            sh "mvn -s settings.xml checkstyle:checkstyle"
           }
       }
       stage('Sonar Analysis') {
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps {
               withSonarQubeEnv("${SONARSERVER}") {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
       }
       stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage("UploadArtifact"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                  groupId: 'QA',
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: "${RELEASE_REPO}",
                  credentialsId: "${NEXUS_LOGIN}",
                  artifacts: [
                    [artifactId: 'vproapp',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
                  ]
                )
            }
        }
    }
}
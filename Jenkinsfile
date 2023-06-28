pipeline {
	agent any
    environment{
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUSIP = "10.0.1.23"
        NEXUSPORT= "8081"
        NEXUS_USER = "admin"
        NEXUS_PASS = "admin123"
        RELEASE_REPO = "vprofile-release"
        CENTRAL_REPO = "vpro-maven-central"
	    NEXUS_GRP_REPO = "vpro-maven-group"
        NEXUS_LOGIN = "nexuslogin"
    }
    stages{
       stage("Build") {
         steps {
           sh 'mvn -s settings.xml -DskipTests install'
         }
         post{
            success{
                echo "archive artifacts"
                archiveArtifacts archive: '**/*.war'
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
    }
}
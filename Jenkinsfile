pipeline {
    agent any
    tools {
        maven 'Maven-3.8.8'
        jdk 'jdk17'
    }
    environment {
        TOMCAT_CREDS=credentials('tomcat-creds') //coming from jenkins creds
        NEXUS_VERSION= "nexus3"
        NEXUS_PROTOCOL= "http"
        NEXUS_URL= "http://34.125.253.15:8081"
        NEXUS_REPO= "allrepo-release" //create in nexus
    }
    stages{
        stage('Build') {
            steps {
                echo "Build in progress"
                sh "mvn clean package -Dmaven.test.failure.ignore=true"
                //-Dcheckstyle.skip
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.war',followSymlinks: false
                }
            }
        }
        stage ('Nexus') {
            steps {
                script {
                 pom = readMavenPom file: "pom.xml"
                 // we need to read pom to see what packages we have,artifact id, version, snapshot/release
                 files = findFiles(glob:"target/*.${pom.packaging}") //what ever is package we will get it here either it is jar or war file
                 //for verification
                 echo "echo ${files[0].name} ${files[0].path} ${files[0].directory} ${files[0].length} ${files[0].lastModified}"
                 //get the path location
                 artifactPath = files[0].path;
                 //validating if file exists and storing in a variable
                 artifactExists = fileExists artifactPath;
                 echo "If artifact exists"
                 echo "${artifactExists}"
                 if(artifactExists) {
                    //run this piece of code
                    echo "Artificat is available going to deploy to nexus"
                    echo "File is : ${artifactPath}, Package is : ${pom.packaging}, Version is : ${pom.version}, GroupId is : ${pom.groupId}"
                    //we need to deploy to nexus using a plugin called as nexus Artifact uploader
                    nexusArtifactUploader {
                        nexusVersion: "${env.NEXUS_VERSION}",
                        protocol : "${env.NEXUS_PROTOCOL}",
                        nexusUrl: "${env.NEXUS_URL}", //
                        groupId: "${pom.groupId}",
                        version: "${BUILD_NUMBER}",
                        repository: "${env.NEXUS_REPO}",
                        credentials="nexus-creds",
                           artifacts: [
                            [
                            artifactId: pom.artifactId,
                            type: pom.packaging,
                            classifier: '',
                            file: artifactPath
                           ]
                           ]
                    }
                 }
                 else {
                    error "*********${artifactPath} is not available ******"
                 }
                  }
               
            }
        }
        stage('Deploy to tomcat') {
            steps {
              //curl commands 
              sh "curl -v -u ${TOMCAT_CREDS_USR}:${TOMCAT_CREDS_PSW}  -T /home/admin/jenkins/workspace/Tomcatdeployment_all/${artifactPath} 'http://34.125.100.123:8080/manager/text/deploy?path=/foo/spring-hello&update=true'"
            }
        }
    }
}

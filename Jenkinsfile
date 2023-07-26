pipeline {
    agent any
    tools {
        maven 'Maven-3.8.8'
        jdk 'jdk17'
    }
    environment {
        TOMCAT_CREDS=credentials('tomcat-creds') //coming from jenkins creds
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
                  }
               
            }
        }
        stage('Deploy to tomcat') {
            steps {
              //curl commands 
              sh "curl -v -u ${TOMCAT_CREDS_USR}:${TOMCAT_CREDS_PSW}  -T /home/admin/jenkins/workspace/Tomcatdeployment_all/target/spring3-mvc-maven-xml-hello-world-1.0-SNAPSHOT.war 'http://34.125.100.123:8080/manager/text/deploy?path=/foo/spring-hello'"
            }
        }
    }
}

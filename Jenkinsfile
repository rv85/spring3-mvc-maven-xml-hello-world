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
        stage('Deploy to tomcat') {
            steps {
              //curl commands 
              sh "curl -v -u ${TOMCAT_CREDS_USR}:${TOMCAT_CREDS_PSW}  -T /home/admin/jenkins/workspace/Tomcatdeployment_all/target/spring3-mvc-maven-xml-hello-world-1.0-SNAPSHOT.war 'http://34.125.100.123:8080/manager/text/deploy?path=/foo/spring-hello'"
            }
        }
    }
}

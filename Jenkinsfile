pipeline {
    agent any
    tools {
        maven 'Maven-3.8.8'
        jdk 'jdk17'
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
              sh "curl -v -u sivaacademy:Devops@12345  -T /var/lib/jenkins/workspace/Tomcatdeployment_allbranch/target/spring-petclinic-3.1.0-SNAPSHOT.war 'http://34.125.100.123:8080/manager/text/deploy?path=/foo/spring-hello'"
            }
        }
    }
}

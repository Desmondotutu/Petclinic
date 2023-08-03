pipeline {
    agent any
    tools{
        maven 'maven3'
    }
    environment{
        SCANNER_HOME= tool 'SonarQubeScanner5.0'
    }

    stages {
        stage('git-checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/Desmondotutu/Petclinic.git'
            }
        }

        stage('Code-Compile') {
            steps {
               sh "mvn clean compile"
            }
        }
        
		stage('OWASP Dependency Check') {
            steps {
               dependencyCheck additionalArguments: '', odcInstallation: 'DP-check'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }


        stage('Sonar Analysis') {
            steps {
               withSonarQubeEnv('SonarqubeServer10'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=java \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=java'''
               }
            }
        }

		 stage('trivy') {
            steps {
               sh "trivy trivy image imagename"
            }
        }

        stage('Code-Build') {
            steps {
               sh "mvn clean install"
            }
        }

         stage('Docker Build') {
            steps {
               script{
                   withDockerRegistry(credentialsId: '9ea0c4b0-721f-4219-be62-48a976dbeec0') {
                    sh "docker build -t  petclinic . "
                 }
               }
            }
        }

        stage('Docker Push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: '9ea0c4b0-721f-4219-be62-48a976dbeec0') {
                    sh "docker tag devopscicd adijaiswal/petclinic:latest"
                    sh "docker push  adijaiswal/petclinic:latest "
                 }
               }
            }
        }
		stage('Deploy to Tomcat') {
            steps {
               sh "  cp /home/ubuntu/myagent/_work/5/s/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/ "
            }
        }

    }
}

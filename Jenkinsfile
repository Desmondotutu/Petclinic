pipeline {
    agent any
    environment {
        mvn = tool 'maven3'
        DependencyCheck = tool 'DP-Check'
    }
     stages{
        stage('Code-Compile') {
            steps {
                sh "mvn clean compile"
            }
        }

        stage('OWASP Dependency Check') {
            steps {
               sh "${DependencyCheck}/bin/dependency-check.sh --scan . dependencyCheckPublisher pattern: '**/dependency-check-report.xml'"
            }
        }

        stage('Static Code Analysis') {

      steps {
        withSonarQubeEnv('SonarqubeServer10') {
          sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=petclinic"
        }
      }
    }

        stage('Code-Build') {
            steps {
                sh "mvn clean install"
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t petclinic ."
                sh "docker tag petclinic desmondo1/myimages:latest"
            }
        }

        stage('trivy') {
            steps {
                sh "trivy image petclinic"
            }
        }
         stage('Push image to Hub'){
            steps{
                script{
                  withDockerRegistry(credentialsId: 'dockerHubCredentials'){
                   sh 'docker push desmondo1/myimages:latest'
                    }
                }
            }
        }
    }
}

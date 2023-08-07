pipeline {
    agent any
    environment {
        mvn = tool 'maven3'
        DependencyCheck = tool 'DP-Check'
    }
     stages{
        stage('Code Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        stage('Code Unit Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('OWASP Dependency Check') {
            steps {
               sh "${DependencyCheck}/bin/dependency-check.sh --scan . dependencyCheckPublisher pattern: '**/dependency-check-report.xml'"
            }
        }

        stage('Sonarqube Static Code Analysis') {

      steps {
        withSonarQubeEnv('SonarqubeServer10') {
          sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=petclinic"
        }
      }
    }

        stage('Code Build') {
            steps {
                sh "mvn clean install"
            }
        }

        stage('Docker Image Build') {
            steps {
                sh "docker build -t petclinic ."
                sh "docker tag petclinic desmondo1/myimages:latest"
            }
        }

        stage('Trivy Docker Image Scan') {
            steps {
                sh "trivy image petclinic"
            }
        }
         stage('Push Image to DockerHub'){
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

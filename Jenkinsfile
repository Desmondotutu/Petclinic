pipeline {
    agent any
    environment {
        maven = tool 'maven3'
        SCANNER_HOME = tool 'SonarQubeScanner5.0'
    }
     stages{
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
                script {
                    withSonarQubeEnv('SonarqubeServer10') {
                        sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=petclinic"
                    }
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
                script {
                    docker.withRegistry('https://docker.io/desmondo1/myimages', 'dockerHubCredentials') {
                        sh "docker build -t petclinic ."
                    }
                }
            }
        }

        stage('trivy') {
            steps {
                sh "trivy image petclinic"
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('https://docker.io/desmondo1/myimages', 'dockerHubCredentials') {
                        sh "docker tag petclinic desmondo1/images:latest"
                        sh "docker push desmondo1/images:latest"
                    }
                }
            }
        }
    }
}

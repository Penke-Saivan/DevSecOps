pipeline {
    agent any

    tools {
        nodejs 'nodejs23'
    }
    environment {
        SCANNER_HOME = tool 'sonar-8.0'
    }
    stages {
        stage('git checkout') {
            steps {
                git branch: 'live', url: 'https://github.com/Penke-Saivan/DevSecOps'
            }
        }
        stage('Frontend Compilation ') {
            steps {
                dir('client') {
                    sh 'find . -name "*.js" -exec node --check {} +'
                }
            }
        }
        stage('BAckend Compilantion') {
            steps {
                dir('api') {
                    sh 'find . -name "*.js" -exec node --check {} +'
                }
            }
        }
        stage('GitLeaks Scan') {
            steps {
                sh 'gitleaks detect --source ./client --exit-code 1'
                sh 'gitleaks detect --source ./api --exit-code 1'
            }
        }

        stage('SonarQube-Scanner-Agent') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    // some block
                    sh """ $SCANNER_HOME/bin/sonar-scanner  -Dsonar.projectName=NodeJS-Project \
                     -Dsonar.projectKey=NodeJS-Project"""
                }
            }
        }
        stage('Quality_gates') {
            steps {
                timeout(60) {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-secret'
                }
            }
        }
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Build-Tag & Push Backend Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        dir('api') {
                            sh 'docker build -t tonyduck/backend:v1 .'
                          
                            sh 'docker push tonyduck/backend:v1'
                           
                        }
                    }
                }
            }
        }  
            
        stage('Build-Tag & Push Frontend Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        dir('client') {
                            sh 'docker build -t tonyduck/frontend:v1 .'
                           
                            sh 'docker push tonyduck/frontend:v1'
                        }
                    }
                }
            }
             
        }  

      
    }
}

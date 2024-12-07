 pipeline {
    agent any
    
    tools{
        
        jdk 'jdk17'
    }
    
    environment {
        
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout ') {
            steps {
                git changelog: false, poll: false, url: 'https://github.com/harishasapu/DotNet-DEMO.git'
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: ' --scan ./ ', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Trivy FS SCan') {
            steps {
                sh "trivy fs ."
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                
                withSonarQubeEnv('SonarQube'){
                  sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=dotnet-demo \
                    -Dsonar.projectKey=dotnet-demo ''' 
               }
            }
        }
        stage("Docker Build & Tag"){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-cred'){
                        sh "make image"
                    }
                }
            }
        }
        stage("Docker Push"){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-cred'){
                        sh "make push"
                    }
                }
            }
        }
        stage("Docker Deploy"){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-cred'){
                        sh "docker run -d --name dotnetapp -p 5000:5000 harishasapu/dotnet-demoapp"
                    }
                }
            }
        }
    }
}


## make sure you need to install make
## sudo yum install make
## git hub Repo link = https://github.com/harishasapu/DotNet-DEMO.git
## you need to change your user name in makefile

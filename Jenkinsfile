pipeline {
    agent any

    tools {
        // Install the Maven version configured as "Maven3"
        maven "maven3"
        jdk "jdk11"
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                // Get some code from a GitHub repository
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/hyndavinandurii/Shopping-Cart.git']])
            }
        }
        stage('Compile') {
            steps{
                sh "mvn clean compile"
            }
        }
        stage('Sonarqube Analysis') {
            steps{
                sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://3.93.59.15:9000/ -Dsonar.login=squ_afc8eeeaf8d1b537812fc5f4658b4745c25b822d -Dsonar.projectName=shopping-cart -Dsonar.java.binaries=. -Dsonar.projectKey=shopping-cart'''
            }
        }
        stage('Build Application') {
            steps{
                sh "mvn clean install -DskipTests=true"
            }
        }
        stage('Build and Push') {
            steps{
                script{
                    withDockerRegistry(credentialsId: '2596920f-17f1-4ec4-9ec1-18f01962b0b1', toolName: 'docker') {
                        sh "docker build -t shopping:latest -f docker/Dockerfile ."
                        sh "docker tag shopping:latest venkatahyndavi/shopping:latest"
                        sh "docker push venkatahyndavi/shopping:latest"
                        sh "docker run -d --name shopping-cart -p 8070:8070 venkatahyndavi/shopping:latest"
                    }
                }
            }
        }
        stage('Trigger CD Pipeline') {
            steps{
                build job: "CD_Pipeline" , wait:true
            }
        }
        
        
    }
}

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
                sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://54.85.144.19:9000/ -Dsonar.login=squ_a0ef0effd28ecd5df729f9e522d92c0e8667afed -Dsonar.projectName=shopping-cart -Dsonar.java.binaries=. -Dsonar.projectKey=shopping-cart'''
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
                    withDockerRegistry(credentialsId: '856fb3bf-2a2d-4a59-bbdc-98d86e2afdad', toolName: 'docker') {
                        sh "docker build -t shopping:latest -f docker/Dockerfile ."
                        sh "docker tag shopping:latest venkatahyndavi/shopping:latest"
                        sh "docker push venkatahyndavi/shopping:latest"
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

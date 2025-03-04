pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
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
                sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://18.209.9.202:9000/ -Dsonar.login=squ_3324c0d969e9ceb4eda1bf285c2fe46f2c19506c -Dsonar.projectName=shopping-cart -Dsonar.java.binaries=. -Dsonar.projectKey=shopping-cart'''
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
                    withDockerRegistry(credentialsId: '0f0fc811-cb0b-4e1b-9c77-7d4b57201da3', toolName: 'docker') {
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

pipeline {
    agent any
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner' // Define SonarQube Scanner tool using 'tool' step
    }

    stages {
        stage('Git') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/ratnakottara/Shopping-Cart.git'
            }
        }
        stage('Compile') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('SAST') {
            steps {
                script {
                    withSonarQubeEnv('sonarqube') {
                        sh "${env.SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=shopping-cart -Dsonar.sources=. -Dsonar.java.binaries=target/classes -Dsonar.host.url=http://107.22.126.73:9000 -Dsonar.login=squ_f95b9103789620cb4fcafa3063882193cbe6536f "
                    }
                }
            }
        }
        stage('OWASP Scan'){
	        steps{
	            dependencyCheck additionalArguments: ' --scan ./', odcInstallation: 'DP'
	             dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        	}
        }
        stage('Build App'){
        	steps{
	             sh "mvn clean install"
        	}
        }
        stage('Build & push DockerImage'){
	        steps{
	            script{
		            withDockerRegistry(credentialsId: 'docker-creds', toolName: 'docker') {
                        sh "docker build -t shopping:latest -f docker/Dockerfile ."
                        sh "docker tag shopping:latest venkatahyndavi/shopping:latest"
                        sh "docker push venkatahyndavi/shopping:lates"
                    }
	            }
        	}
        }
    }
}
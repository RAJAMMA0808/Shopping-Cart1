pipeline {
    agent any
    tools{
        jdk  'jdk17'
        nodejs 'node16'
        maven  'maven3'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/RAJAMMA0808/Shopping-Cart1.git'
            }
        }
        
        stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
			
        }
        
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Shopping-Cart1 \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Shopping-Cart1 '''
               }
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
		stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "docker build -t shopping-cart1 ."
                       sh "docker tag shopping-cart1 raji0808/shopping-cart1:latest "
                       sh "docker push raji0808/shopping-cart1:latest "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image raji0808/shopping-cart1:latest > trivy.txt" 
            }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -d --name shopping-cart1 -p 8082:8082 raji0808/shopping-cart1:latest'
            }
        }
    }
}

pipeline {
    agent any
    tools{
        jdk  'jdk17'
        maven  'maven3'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: '1b315952-5c09-4ff3-bba1-ee58809e39b7', poll: false, url: 'https://github.com/RAJAMMA0808/Shopping-Cart1.git'
            }
        }
        
        stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '', odcInstallation: 'DP'
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
        
        stage('Docker Build & Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: '0c988655-89ee-4f54-a123-a8b80ff41229', toolName: 'docker') {
                        sh "docker build -t shopping-cart1 -f docker/Dockerfile ."
                        sh "docker tag  shopping-cart raji0808/shopping-cart1:latest"
                        sh "docker push raji0808/shopping-cart1:latest"
                    }
                }
            }
        }
        stage('Deploy') {
		    steps {
			    withDockerRegistry(credentialsId: '0c988655-89ee-4f54-a123-a8b80ff41229', toolName: 'docker') {
		           sh "docker run -d --name-shop-shop -p 8070:8070 raji0808/shopping-cart1:latest"
                    }
		 }
            }
	}	 
    }
}

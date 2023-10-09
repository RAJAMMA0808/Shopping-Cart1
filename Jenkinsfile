pipeline {
    agent any
    tools{
        jdk  'jdk11'
        maven  'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: '2b195baf-934e-4987-9147-1028eb1e695c', poll: false, url: 'https://github.com/RAJAMMA0808/Shopping-Cart.git'
            }
        }
        
        stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Shopping-Cart \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Shopping-Cart '''
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
                        sh "docker build -t shopping-cart -f docker/Dockerfile ."
                        sh "docker tag  shopping-cart raji0808/shopping-cart:latest"
                        sh "docker push raji0808/shopping-cart:latest"
                    }
                }
            }
        }
        stage('Deploy') {
		    steps {
			    withDockerRegistry() {
				     
					 sh "docker run -d --name-shop-shop -p 8070:8070 raji0808/shopping-cart:latest"
                    }
		        }
            }
	    }	 
    }
}
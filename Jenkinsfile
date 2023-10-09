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
                git branch: 'main', changelog: false, credentialsId: '54751a09-0a11-42ed-81de-4568b859cb8b', poll: false, url: 'https://github.com/RAJAMMA0808/Shopping-Cart.git'
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
                    withDockerRegistry(credentialsId: '7f43e420-0f46-4294-bb4c-7945fb74e35c', toolName: 'docker') {
                        
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

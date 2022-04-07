pipeline {

    agent any

    environment {
  		PASS = credentials('registry-pass')
		AWS_REGION = "us-east-1"
		CLUSTER_NAME = "eks-cluster-name"
        REGISTRY = "anandmaragur"
		
		//AWS Credentials must set at jenkins and same being used at withAWS block!
    }

    stages {
	
	    stage('Checkout') {
			checkout scm
        }

        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'

            }
            post {
                success {
                    archiveArtifacts artifacts: 'build/simple-java-maven-app/target/*.jar', fingerprint: true
                }
            }
        }  
		
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit './target/surefire-reports/*.xml'
                }
            }
        }
		
		//Sonar setup is required!
		stage('Scan'){
          try {
            sh "mvn sonar:sonar"
              } catch(error){
                echo "The sonar server could not be reached ${error}"
               }
        }
		
        stage('Push') {
            steps {
			
                sh '''
					echo "*** Logging in ***"
					echo $PASS  | docker login --username anandmaragur --password-stdin
					docker build . -t $REGISTRY/redis-counter-app:v1 --no-cache
					echo "*** Pushing image ***"
					docker push $REGISTRY/redis-counter-app:v1
					'''
            }
        }
        stage('Deploy') {
        	steps {
			//AWS plugin is required for this.
			   withAWS(credentials: 'aws-credentials', region: $AWS_REGION ) { 
        		sh 'aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME'
				sh 'kubectl apply -f ./manifests/*.yml'
				
				}
        	}
        }
    }
}

pipeline {
    agent {
		label 'Ranjeet_node'
    }
    environment {
        VENV = "venv"
        MONGO_URI = credentials('mongo-uri')
		SECRET_KEY = credentials('flask-secret')
    }

    stages {

        stage('Checkout') {

            steps {
                echo 'Checking out source code...'
				cleanWs()
                checkout scm 
            }
        }

        stage('Build') {
            steps {
                echo 'Installing dependencies...'
                
                sh '''
                    python3 -m venv venv

                    . venv/bin/activate

                    python -m pip install --upgrade pip

                    python -m pip install -r requirements.txt
                '''
            }
        }
        stage('Test') {
            steps {
                echo 'Running unit tests...'

                sh '''
                    . venv/bin/activate

                    export PYTHONPATH=$WORKSPACE

                    python -m pytest -v
                '''
            }
        } 

        stage('Deploy') {
            steps {
                sh '''
                    cd /home/ubuntu/apps/flask-cicd-demo

                    git fetch origin
                    git reset --hard origin/main

                    . venv/bin/activate
                    python -m pip install -r requirements.txt

                    sudo systemctl restart flask-demo

                    sleep 5

                    curl http://localhost:8000/health
                '''
            }
        }
    }

	post {
	    success {
			echo "Build completed successfully."
	        emailext(
	            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
	            body: """
					Hello,
					
					Your Jenkins build completed successfully.
					
					Job Name     : ${env.JOB_NAME}
					Build Number : ${env.BUILD_NUMBER}
					Build Status : SUCCESS
					
					Build URL:
					${env.BUILD_URL}
					
					Regards,
					Jenkins CI/CD
					""",
	            to: "chandhini1940@outlook.com"
	        )
	    }
	
	    failure {
			echo "Build failed."
	        emailext(
	            subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
	            body: """
					Hello,
					
					Your Jenkins build has failed.
					
					Job Name     : ${env.JOB_NAME}
					Build Number : ${env.BUILD_NUMBER}
					Build Status : FAILED
					
					Build URL:
					${env.BUILD_URL}
					
					Please check the console output for details.
					
					Regards,
					Jenkins CI/CD
					""",
	            to: "chandhini1940@outlook.com"
	        )
	    }
	}
}
pipeline {

    agent any

    triggers {
        githubPush()
    }

    environment {
        MONGO_URI  = credentials('mongo-uri')
        SECRET_KEY = credentials('flask-secret')
    }

    stages {

        stage('Build') {
            steps {
                echo '=== BUILD STAGE ==='
                echo 'Installing Python dependencies...'

                sh '''
                  python3 --version
                  python3 -m venv venv
                  . venv/bin/activate
                  pip install --upgrade pip
                  pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo '=== TEST STAGE ==='
                echo 'Running unit tests...'

                sh '''
                  . venv/bin/activate
                  python -m pytest -v
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo '=== DEPLOY STAGE ==='
                echo 'Deploying application to staging environment...'

                sh '''
                  rm -rf staging
                  mkdir -p staging

                  cp -r templates staging/
                  cp app.py staging/
                  cp requirements.txt staging/

                  echo "Application deployed successfully to staging"
                  ls -R staging
                '''
            }
        }
    }

    post {

        success {
            echo '=== PIPELINE SUCCESS ==='
            emailext(
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Jenkins pipeline completed successfully.

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: SUCCESS

Build URL:
${env.BUILD_URL}
""",
                to: 'chandhini1940@outlook.com'
            )
        }

        failure {
            echo '=== PIPELINE FAILED ==='
            emailext(
                subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Jenkins pipeline failed.

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: FAILURE

Please check the Jenkins console output.

Build URL:
${env.BUILD_URL}
""",
                to: 'chandhini1940@outlook.com'
            )
        }

        always {
            echo '=== PIPELINE COMPLETED ==='
        }
    }
}
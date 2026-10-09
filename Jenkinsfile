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
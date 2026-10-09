pipeline {
agent any

```
triggers {
    githubPush()
}

options {
    skipDefaultCheckout(true)
    timestamps()
}

environment {
    MONGO_URI  = credentials('mongo-uri')
    SECRET_KEY = credentials('flask-secret')
    VENV_DIR   = 'venv'
}

stages {

    stage('Checkout') {
        steps {
            echo '=== CHECKOUT STAGE ==='
            checkout scm
        }
    }

    stage('Build') {
        steps {
            echo '=== BUILD STAGE ==='
            echo 'Installing Python dependencies...'

            sh '''
                set -eu

                python3 --version

                # Start with a clean virtual environment
                rm -rf "$VENV_DIR"
                python3 -m venv "$VENV_DIR"

                # Install dependencies using the venv interpreter
                "$VENV_DIR/bin/python" -m pip install --upgrade pip
                "$VENV_DIR/bin/python" -m pip install -r requirements.txt

                # Ensure pytest is available
                "$VENV_DIR/bin/python" -m pip install pytest

                echo "Build and dependency installation completed."
            '''
        }
    }

    stage('Test') {
        steps {
            echo '=== TEST STAGE ==='
            echo 'Running unit tests...'

            sh '''
                set -eu

                "$VENV_DIR/bin/python" -m pytest -v
            '''
        }
    }

    stage('Deploy') {
        steps {
            echo '=== DEPLOY STAGE ==='
            echo 'Preparing staging package...'

            sh '''
                set -eu

                # Verify required application files
                test -f app.py
                test -f requirements.txt
                test -d templates

                # Prepare staging directory
                rm -rf staging
                mkdir -p staging

                # Copy the Flask application
                cp app.py staging/
                cp requirements.txt staging/
                cp -r templates staging/

                # Include static assets if present
                if [ -d static ]; then
                    cp -r static staging/
                fi

                echo "Staging package created successfully."
                find staging -maxdepth 3 -type f -print
            '''
        }
    }
}

post {
    success {
        echo '=== PIPELINE SUCCESS ==='

        emailext(
            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """Jenkins pipeline completed successfully.
```

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: SUCCESS

Staging package prepared in the Jenkins workspace.

Build URL:
${env.BUILD_URL}
""",
to: '[chandhini1940@outlook.com](mailto:chandhini1940@outlook.com)'
)
}

```
    failure {
        echo '=== PIPELINE FAILED ==='

        emailext(
            subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """Jenkins pipeline failed.
```

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: FAILURE

Please check the Jenkins Console Output for the first error.

Build URL:
${env.BUILD_URL}
""",
to: '[chandhini1940@outlook.com](mailto:chandhini1940@outlook.com)'
)
}

```
    always {
        echo '=== PIPELINE COMPLETED ==='

        // Remove the virtual environment, retaining the staging package
        sh 'rm -rf venv'
    }
}
```

}

pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS_ID = 'azure-service-principal'
        RESOURCE_GROUP = 'myResourceGroup'
        APP_SERVICE_NAME = 'myPythonBrijesh002'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/brijeshprajapati53/WebApplicationForJenkins.git'
            }
        }

        stage('Set Up Python') {
            steps {
                bat 'python -m venv venv'
                bat 'call venv\\Scripts\\activate && pip install -r requirements.txt'
            }
        }

       stage('Publish') {
            steps {
                bat '''
                powershell Compress-Archive -Path * -DestinationPath app.zip -Force
                '''
            }
        }

        
        stage('Deploy to Azure') {
            steps {
                bat 'az webapp up --name %APP_SERVICE_NAME% --resource-group %RESOURCE_GROUP% --runtime "PYTHON:3.9" --sku B1'
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}

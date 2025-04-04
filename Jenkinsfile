pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS = credentials('azure-service-principal')
        RESOURCE_GROUP = 'myResourceGroup'
        APP_SERVICE_NAME = 'myPythonBrijesh002'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/brijeshprajapati53/PythonApplicationForJenkins.git'
            }
        }

        stage('Build') {
            steps {
                bat 'pip install -r requirements.txt'
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
                withCredentials([azureServicePrincipal('azure-service-principal')]) {
                    bat '''
                    set AZURE_USER=%AZURE_CREDENTIALS_USR%
                    set AZURE_PASS=%AZURE_CREDENTIALS_PSW%
                    set AZURE_TENANT=%AZURE_CREDENTIALS_TEN%

                    az login --service-principal -u %AZURE_USER% -p %AZURE_PASS% --tenant %AZURE_TENANT%
                    az webapp deploy --resource-group %RESOURCE_GROUP% --name %APP_SERVICE_NAME% --src-path app.zip --type zip
                    '''
                }
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

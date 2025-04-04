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
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Publish') {
            steps {
                sh 'zip -r app.zip .'
            }
        }

        stage('Deploy to Azure') {
            steps {
                withCredentials([azureServicePrincipal('azure-service-principal')]) {
                    sh 'az login --service-principal -u $AZURE_CREDENTIALS_USR -p $AZURE_CREDENTIALS_PSW --tenant $AZURE_CREDENTIALS_TEN'
                    sh 'az webapp deploy --resource-group $RESOURCE_GROUP --name $APP_SERVICE_NAME --src-path app.zip --type zip'
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

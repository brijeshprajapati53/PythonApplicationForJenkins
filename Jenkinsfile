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
                sh 'python -m venv venv'
                sh '. venv/bin/activate && pip install -r requirements.txt'
            }
        }

        stage('Test App') {
            steps {
                sh '. venv/bin/activate && python app.py &'
                sh 'sleep 5 && curl http://127.0.0.1:5000'
            }
        }

        stage('Login to Azure') {
            steps {
                withCredentials([azureServicePrincipal("${AZURE_CREDENTIALS_ID}")]) {
                    sh '''
                        az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID
                        az account set --subscription $AZURE_SUBSCRIPTION_ID
                    '''
                }
            }
        }

        stage('Deploy to Azure') {
            steps {
                sh 'az webapp up --name $APP_NAME --resource-group $RESOURCE_GROUP --runtime "PYTHON:3.9" --sku B1'
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

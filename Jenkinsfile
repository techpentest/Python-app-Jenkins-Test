pipeline {
    agent any
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/techpentest/Python-app-Jenkins-Test.git'
            }
        }
        
stage('Setup Virtual Environment') {
    steps {
        sh '''
        rm -rf venv  # Remove any existing virtual environments
        python3 -m venv venv  # Create a new virtual environment
        chmod -R 755 venv
        bash -c "
        source venv/bin/activate
        pip install --upgrade pip
        pip install -r requirements.txt
        "
        '''
    }
}
   
         stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                          sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Python-project \
                          -Dsonar.projectName=Python-project \
                          -Dsonar.exclusions=venv/** \
                          -Dsonar.sources=. \
                          -Dsonar.python.coverage.reportPaths=coverage.xml'''
                }
            }
        }
        
        stage('Quality Gateway Check') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: false,
                    credentialsId: 'sonar-token'
                }
            }
        }
    }
}

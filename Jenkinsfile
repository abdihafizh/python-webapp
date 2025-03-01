pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://192.168.1.50:9000/'
        SONARQUBE_SCANNER = 'SonarScanner'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/abdihafizh/python-webapp.git'
            }
        }

        stage('Setup Virtual Environment') {
            steps {
                sh 'python3 -m venv venv'
                sh 'bash -c "source venv/bin/activate && pip install --upgrade pip"'
                sh 'bash -c "source venv/bin/activate && pip install -r requirements.txt"'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'bash -c "source venv/bin/activate && pytest --junitxml=report.xml"'
            }
        }

        stage('Code Analysis with SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube-RPI') {
                    sh '''
                    sonar-scanner \
                      -Dsonar.projectKey=jenkins-cicd \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=${SONARQUBE_URL} \
                      -Dsonar.login=${SONAR_AUTH_TOKEN}
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'bash -c "source venv/bin/activate && nohup python3 app.py &"'
            }
        }
    }
}

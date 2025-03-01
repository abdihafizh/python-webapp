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
                sh 'source venv/bin/activate'
                sh 'pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'source venv/bin/activate && pytest --junitxml=report.xml'
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
                      -Dsonar.login=sqp_b6b0ee93d5c691f267fc927013c0fee4ee27e055
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'source venv/bin/activate && nohup python3 app.py &'
            }
        }
    }
}

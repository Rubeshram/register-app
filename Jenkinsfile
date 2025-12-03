pipeline {
    agent { label 'Jenkins-Agent' }

    tools {
        jdk 'Java17'
        maven 'Maven3'
    }

    stages {

        stage('Cleanup Workspace') {
            steps { cleanWs() }
        }

        stage('Checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github',
                    url: 'https://github.com/Rubeshram/register-app'
            }
        }

        stage('Build Application') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('Test Application') {
            steps {
                sh "mvn test"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.projectKey=register-app \
                        -Dsonar.host.url=http://YOUR-SONARQUBE:9000 \
                        -Dsonar.login=$SONAR_AUTH_TOKEN
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
    }
}

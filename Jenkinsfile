pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'sonarqube' // Nombre configurado en Jenkins
    }

    stages {
        stage('Validar Conexión con SonarQube') {
            steps {
                script {
                    echo "Validando conexión con SonarQube..."
                    def status = sh(script: "curl -s -o /dev/null -w '%{http_code}' http://sonarqube:9000", returnStdout: true).trim()
                    if (status != '200') {
                        error "SonarQube no está disponible en http://sonarqube:9000. Código de estado: ${status}"
                    }
                }
            }
        }
        stage('Análisis de Código con SonarQube') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    withCredentials([string(credentialsId: 'sonarqube', variable: 'SONARQUBE_TOKEN')]) {
                        script {
                            def scannerHome = tool 'sonarqube'
                            sh """
                                ${scannerHome}/bin/sonar-scanner \
                                -Dsonar.projectKey=jenkins \
                                -Dsonar.sources=. \
                                -Dsonar.host.url=http://sonarqube:9000 \
                                -Dsonar.login=$SONARQUBE_TOKEN
                            """
                        }
                    }
                }
            }
        }
    }
}

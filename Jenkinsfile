#!/usr/bin/groovy

pipeline {
    agent any
    stages {
        stage("sonar quality check") {
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonartoken') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube \
                          -Dsonar.host.url=${env.SONAR_HOST_URL} \
                          -Dsonar.login=${env.SONAR_AUTH_TOKEN}'
                    }

                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }

                }
            }
        }
    }
}

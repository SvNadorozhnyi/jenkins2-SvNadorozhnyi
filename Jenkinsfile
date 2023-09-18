pipeline {
    agent any
    environment {
        APP_PORT = 9090
        JOB_NAME = "${env.JOB_NAME}"
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B package -DskipTests'
            }
        }
        stage('Integration Test') {
            parallel {
                stage('Running Application') {
                    agent any
                    options {
                        timeout(time: 60, unit: 'SECONDS')
                    }
                    steps {
                        script {
                            try {
                                dir("../${JOB_NAME}/target") {
                                    sh 'java -jar contact.war'
                                }
                            } catch (Exception e) {
                                currentBuild.result = "SUCCESS"
                            }
                        }
                    }
                }
                stage('Running Test') {
                    steps {
                        sleep(time: 30, unit: 'SECONDS')
                        sh 'mvn -Dtest=RestIT test'
                    }
                }
            }
        }
    }
}

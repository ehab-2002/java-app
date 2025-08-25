// Jenkinsfile (Declarative + Scripted + SharedLib + Parameters + Parallel)
@Library('ci-shared-lib') _  // اسم الـ Shared Library اللي هتعمله

pipeline {
    agent any

    // Parameters for build
    parameters {
        string(name: 'DOCKER_IMAGE', defaultValue: 'ehabfarid1/java-app', description: 'Docker image name')
        booleanParam(name: 'SKIP_TESTS', defaultValue: true, description: 'Skip Maven tests?')
    }

    environment {
        MAVEN_HOME = tool 'Maven3'
        JAVA_HOME = tool 'Java11'
    }

    stages {
        stage('Build') {
            parallel {
                stage('Build Jar') {
                    steps {
                        script {
                            def skipTests = params.SKIP_TESTS ? '-DskipTests' : ''
                            sh "${MAVEN_HOME}/bin/mvn clean package ${skipTests}"
                        }
                    }
                }
                stage('Static Analysis') {
                    steps {
                        script {
                            echo "Running static analysis..."
                            // ممكن تحط أي أدوات زي SonarQube هنا
                        }
                    }
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                       // Build Docker Image
                       dockerHelper.buildImage("${params.DOCKER_IMAGE}-${env.BUILD_NUMBER}")
            
            // Login to DockerHub
                      dockerHelper.loginDocker('dockerhub-cred')
            
            // Push Docker Image
                      dockerHelper.pushImage("${params.DOCKER_IMAGE}-${env.BUILD_NUMBER}")
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo "Pipeline succeeded!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}


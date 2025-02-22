pipeline {
    agent any

    environment {
        MAVEN_HOME = tool 'maven'
        DOCKER_HOME = tool 'docker'
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    echo 'Initializing all the variables...'
                    mavenCMD = "${MAVEN_HOME}/bin/mvn"
                    dockerCMD = "${DOCKER_HOME}/bin/docker"
                    tagName = "3.0"
                }
            }
        }

        stage('Git Code Checkout') {
            steps {
                script {
                    echo 'Checking out the code from the Git repository...'
                    try {
                        git 'https://github.com/shrikant-devops/healthcare-project-shrikant.git'
                    } catch (Exception e) {
                        echo "Error during Git checkout: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        error("Git checkout failed") // This will fail the build
                    }
                }
            }
        }

        stage('Compile and Test') {
            steps {
                script {
                    echo 'Compiling and testing the project...'
                    sh "${mavenCMD} clean package" // Use the defined mavenCMD variable
                }
            }
        }

            stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t shrikantdevops/healthcare:latest .'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                   kubernetesDeploy (configs: 'deploy.yml' ,kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

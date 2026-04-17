pipeline {
    agent any

    parameters {
        string(name: 'GIT_BRANCH', defaultValue: 'main', description: 'Enter the branch name')
        string(name: 'HOST_PORT', defaultValue: '', description: 'HOST_PORT')
        string(name: 'CONTAINER_NAME', defaultValue: '', description: 'CONTAINER_NAME')
        string(name: 'IMAGE_NAME', defaultValue: 'dockerhubusername/repo', description: 'Enter your image name')
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'Enter your image tag')
        string(name: 'REMOTE_USER', defaultValue: '', description: 'Enter remote user')
        string(name: 'SERVER_DNS', defaultValue: '', description: 'Enter server DNS')
        booleanParam(name: 'RUN_STAGE', defaultValue: false, description: 'Run build stage')
        booleanParam(name: 'DELETE_APP', defaultValue: false, description: 'Delete app')
        booleanParam(name: 'SKIP_DEPLOYMENT', defaultValue: false, description: 'Skip deployment')
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: "${params.GIT_BRANCH}", url: 'https://github.com/chrisdylan237/Amazon-Prime-.git'
            }
        }

        stage('Build App') {
            when {
                expression { params.RUN_STAGE && !params.DELETE_APP }
            }
            steps {
                script {
                    sh """
                        ls -l
                        docker build -t ${params.IMAGE_NAME}:${params.IMAGE_TAG} .
                    """
                }
            }
        }

        stage('Login to DockerHub') {
            when {
                expression { !params.SKIP_DEPLOYMENT }
            }
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: "Github-dylan",
                        usernameVariable: "DOCKER_USERNAME",
                        passwordVariable: "DOCKER_PASSWORD"
                    )]) {
                        sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            when {
                expression { !params.SKIP_DEPLOYMENT }
            }
            steps {
                script {
                    sh "docker push ${params.IMAGE_NAME}:${params.IMAGE_TAG}"
                }
            }
        }
    }
}

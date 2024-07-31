pipeline {
    agent any
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to build')
    }
    environment {
        DEV_REMOTE_SERVER = 'ubuntu@16.16.211.108'
        DEV_SSH_CREDENTIALS_ID = 'dev_ssh'
        DEV_REMOTE_WORKDIR = '/home/ubuntu/dev/git-learn'
        MAIN_REMOTE_SERVER = 'ubuntu@51.20.252.51'
        MAIN_SSH_CREDENTIALS_ID = 'main_ssh'
        MAIN_REMOTE_WORKDIR = '/home/ubuntu/main/git-learn'
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    git url: 'https://github.com/magesh-do/git-learn.git', branch: "${params.BRANCH_NAME}"
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Building...'
                // Add your build commands here
            }
        }
        stage('Print Branch Name') {
            steps {
                script {
                    echo "Branch Name: ${params.BRANCH_NAME}"
                    echo "Deploy to Dev condition: ${params.BRANCH_NAME == 'dev'}"
                    echo "Deploy to Main condition: ${params.BRANCH_NAME == 'main'}"
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    def remoteServer = ''
                    def remoteWorkdir = ''
                    def sshCredentialsId = ''
                    
                    if (params.BRANCH_NAME == 'dev') {
                        remoteServer = DEV_REMOTE_SERVER
                        remoteWorkdir = DEV_REMOTE_WORKDIR
                        sshCredentialsId = DEV_SSH_CREDENTIALS_ID
                    } else if (params.BRANCH_NAME == 'main') {
                        remoteServer = MAIN_REMOTE_SERVER
                        remoteWorkdir = MAIN_REMOTE_WORKDIR
                        sshCredentialsId = MAIN_SSH_CREDENTIALS_ID
                    }
                    
                    if (remoteServer && remoteWorkdir && sshCredentialsId) {
                        sshagent([sshCredentialsId]) {
                            sh """
                            ssh -o StrictHostKeyChecking=no ${remoteServer} << 'EOF'
                            cd ${remoteWorkdir}
                            git fetch origin
                            git checkout ${params.BRANCH_NAME}
                            git reset --hard origin/${params.BRANCH_NAME}
                            echo "Deployment to ${params.BRANCH_NAME} completed."
                            EOF
                            """
                        }
                    } else {
                        error "Invalid branch name or configuration for deployment."
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Cleaning up...'
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}

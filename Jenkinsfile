pipeline {
    agent any

    environment {
        DOTNET_VERSION = "8.0"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                def branchName = env.GIT_BRANCH
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: "*/${branchName}"]],
                        doGenerateSubmoduleConfigurations: false,
                        extensions: [[$class: 'WipeWorkspace']], // Ensures a fresh pull
                        userRemoteConfigs: [[
                            url: 'https://github.com/ab-80/ResumeParser.git',
                            credentialsId: ''
                        ]]
                    ])
                    echo "Building branch: ${branchName}"
                }
            }
        }

        stage('Setup .NET SDK') {
            steps {
                sh 'dotnet --version || curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel $DOTNET_VERSION'
            }
        }

        stage('Restore Dependencies') {
            steps {
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build --configuration Release'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'dotnet test --configuration Release --no-build'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/bin/Release/**', fingerprint: true
        }
        failure {
            echo 'Build failed!'
        }
        success {
            script {
                echo "Build succeeded on branch: ${env.GIT_BRANCH}"
            }
        }
    }
}
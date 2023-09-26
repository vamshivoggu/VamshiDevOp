pipeline {
    agent any 


    environment {
        YOUR_SECRET_KEY = 'yourActualSecretKeyHere'
    }

    parameters {
        string(name: 'ciBuildVersion', defaultValue: '1.0.0', description: 'Build version')
        choice(name: 'ciBuildEnv', choices: ['prod', 'dev', 'staging'], description: 'Build environment')
    }

    stages {
        stage('Dependencies') {
            steps {
                echo 'Fetching dependencies...'
                sh 'make deps'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'make test'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the binary...'
                script {
                    def ciGitHash = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                    def ciBuildTimestamp = sh(script: 'date +"%Y-%m-%d%H:%M:%S"', returnStdout: true).trim()
                    def ciBuildBranch = env.BRANCH_NAME // This will fetch the branch name for multibranch pipelines, for regular jobs you might need to fetch it from GIT_BRANCH

                    sh """
                        make build ciBuildVersion=${params.ciBuildVersion} ciBuildBranch=${ciBuildBranch} ciBuildEnv=${params.ciBuildEnv} ciGitHash=${ciGitHash} ciBuildTimestamp="${ciBuildTimestamp}"
                    """
                }
            }
        }

 
         


        stage('Package') {
            steps {
                echo 'Packaging the binary...'
                sh 'make package'
            }
        }

        stage('Cleanup') {
            steps {
                echo 'Cleaning up...'
                sh 'make clean'
            }
        }
    }

    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed :('
        }
    }
}


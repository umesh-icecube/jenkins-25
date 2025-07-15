pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-pat'  // Jenkins credentials ID (Username+Password or SSH)
        REPO_URL = 'https://github.com/umesh-icecube/jenkins-25.git'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git credentialsId: "${env.GIT_CREDENTIALS_ID}", url: "${env.REPO_URL}", branch: 'development'
            }
        }

        stage('Push to Staging') {
            steps {
                script {
                    sh '''
                        git config user.email "jenkins@ci.com"
                        git config user.name "Jenkins CI"
                        
                        # Fetch and checkout staging
                        git fetch origin staging
                        git checkout staging

                        # Merge development into staging
                        git merge origin/development -m "CI: Merge development into staging"

                        # Push staging branch
                        git push origin staging
                    '''
                }
            }
        }
    }
}

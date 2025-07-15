pipeline {
    agent any
    environment {
        CRED_ID = 'github-pat'                    // Username+Password (PAT) credential
        REPO    = 'https://github.com/umesh-icecube/jenkins-25.git'
    }
    stages {
        stage('Checkout development') {
            steps {
                git url: REPO, credentialsId: CRED_ID, branch: 'development'
            }
        }
        stage('Merge & push to staging') {
            steps {
                withCredentials([usernamePassword(credentialsId: CRED_ID,
                                                 usernameVariable: 'GIT_USER',
                                                 passwordVariable: 'GIT_TOKEN')]) {
                    sh """
                       set -e
                       git config user.email "jenkins@ci"
                       git config user.name  "Jenkins CI"

                       # Make sure staging is present
                       git fetch origin staging:refs/remotes/origin/staging || true
                       git checkout -B staging origin/staging || git checkout -b staging

                       git merge --no-ff --log origin/development -m "CI: merge development into staging"

                       # Push using PAT
                       git push https://${GIT_USER}:${GIT_TOKEN}@github.com/your-user/your-repo.git staging
                    """
                }
            }
        }
    }
    post {
        failure {
            mail to: 'umesh@icecubedigital.com',
                 subject: "Merge to staging failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Check console output: ${env.RUN_DISPLAY_URL}"
        }
    }
}

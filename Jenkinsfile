pipeline {
    agent {
        docker {
            image 'python:3.10'
        }
    }

    environment {
        VENV = 'venv'
    }
    
    stages {
        stage('Setup Environment & Install Dependencies') {
            steps {
                sh '''
                    python -m venv $VENV
                    . $VENV/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install pytest
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . $VENV/bin/activate
                    pytest test_app.py
                '''
            }
        }

        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: "release/.*", comparator: "REGEXP"
                }
            }
            steps {
                echo "Simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post {
        success {
            script {
                def payload = [
                    content: "✅ Build SUCCESS on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1369197474174074961/d92R5Wvj2_7NOsFNajK-GOmTdZMRDwIlYpNmBHiKZq2YHoD3HUipDuo4uo19nddjGFJZ'
                )
            }
        }
        failure {
            script {
                def payload = [
                    content: "❌ Build FAILED on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1369197474174074961/d92R5Wvj2_7NOsFNajK-GOmTdZMRDwIlYpNmBHiKZq2YHoD3HUipDuo4uo19nddjGFJZ'
                )
            }
        }
    }
}

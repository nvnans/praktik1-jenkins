pipeline {
    agent any

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'pytest test_app.py'
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
                    content: "Build SUCCESS on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST'
                    contentType: 'APPLICATION_JSON'
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1378369090728493116/n2HiD_nwbiWSDFQNLHjCHxBJmQzsy14Gu3PaRIuWEglS9n1XeGFmA2s77gbN9JMTEYCZ'
                )
            }
        }
        failure {
            script {
                def payload = [
                    content: "Build FAILED on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST'
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1378369090728493116/n2HiD_nwbiWSDFQNLHjCHxBJmQzsy14Gu3PaRIuWEglS9n1XeGFmA2s77gbN9JMTEYCZ'
                ) 
            }
        }
    }
}

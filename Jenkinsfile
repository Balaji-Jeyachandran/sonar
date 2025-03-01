pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/Balaji-Jeyachandran/sonar.git'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '/opt/sonar-scanner/bin/sonar-scanner'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    def sonarProjectKey = "python-security-scan"  
                    def sonarUrl = "http://localhost:9000"
                    def sonarToken = "sqp_6a94d44e333017c691170b184ddeb86116c7f0cc"

                    def response = sh(
                        script: """curl -s -u ${sonarToken}: ${sonarUrl}/api/qualitygates/project_status?projectKey=${sonarProjectKey}""",
                        returnStdout: true
                    ).trim()

                    def status = new groovy.json.JsonSlurper().parseText(response).projectStatus.status

                    if (status != 'OK') {
                        error "Quality Gate failed with status: ${status}"
                    } else {
                        echo "Quality Gate passed!"
                    }
                }
            }
        }
    }
}

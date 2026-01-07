pipeline {
    agent any

    environment {
        TOOLBELT = 'sf'
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Authorize Dev Hub (JWT)') {
            steps {
                script {
                    // ✅ Correct way to map Jenkins env vars
                    def HUB_ORG                     = env.HUB_ORG_DH
                    def SFDC_HOST                  = env.SFDC_HOST_DH
                    def CONNECTED_APP_CONSUMER_KEY = env.CONNECTED_APP_CONSUMER_KEY_DH
                    def JWT_KEY_CRED_ID            = env.JWT_CRED_ID_DH

                    withCredentials([
                        file(credentialsId: JWT_KEY_CRED_ID, variable: 'JWT_KEY_FILE')
                    ]) {

                        bat """
                        ${TOOLBELT} auth jwt grant ^
                          --client-id ${CONNECTED_APP_CONSUMER_KEY} ^
                          --jwt-key-file "%JWT_KEY_FILE%" ^
                          --username ${HUB_ORG} ^
                          --instance-url ${SFDC_HOST} ^
                          --set-default-dev-hub
                        """
                    }
                }
            }
        }

        stage('Deploy Metadata') {
            steps {
                bat """
                ${TOOLBELT} project deploy start ^
                  --manifest manifest/package.xml ^
                  --target-org ${env.HUB_ORG_DH}
                """
            }
        }
    }
}

pipeline {
    agent any

    environment {
        WEBEX_BOT_TOKEN = credentials('webex-bot-token')
        WEBEX_ROOM_ID = credentials('webex-room-id')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh 'python3 -m unittest'
            }
        }
    }

    post {
        success {
            sh '''
                curl -sS -X POST https://webexapis.com/v1/messages \
                -H "Authorization: Bearer $WEBEX_BOT_TOKEN" \
                -H "Content-Type: application/json" \
                -d "{\"roomId\":\"$WEBEX_ROOM_ID\",\"text\":\"Jenkins Build #$BUILD_NUMBER SUCCESS\"}"
            '''
        }

        failure {
            sh '''
                curl -sS -X POST https://webexapis.com/v1/messages \
                -H "Authorization: Bearer $WEBEX_BOT_TOKEN" \
                -H "Content-Type: application/json" \
                -d "{\"roomId\":\"$WEBEX_ROOM_ID\",\"text\":\"Jenkins Build #$BUILD_NUMBER FAILED\"}"
            '''
        }
    }
}

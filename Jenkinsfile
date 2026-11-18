pipeline {
    agent any

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

        stage('Notify WebEx') {
            steps {
                withCredentials([
                    string(credentialsId: 'webex-bot-token', variable: 'WEBEX_BOT_TOKEN'),
                    string(credentialsId: 'webex-room-id', variable: 'WEBEX_ROOM_ID')
                ]) {
                    sh '''
                        test -n "$WEBEX_BOT_TOKEN" || { echo "WEBEX_BOT_TOKEN missing"; exit 1; }
                        test -n "$WEBEX_ROOM_ID" || { echo "WEBEX_ROOM_ID missing"; exit 1; }

                        payload=$(printf '{"roomId":"%s","text":"Jenkins Build #%s SUCCESS"}' "$WEBEX_ROOM_ID" "$BUILD_NUMBER")

                        curl -f -sS -X POST 'https://webexapis.com/v1/messages' \
                          -H "Authorization: Bearer $WEBEX_BOT_TOKEN" \
                          -H 'Content-Type: application/json' \
                          --data "$payload"
                    '''
                }
            }
        }
    }
}

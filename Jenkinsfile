pipeline {
    agent any
    environment {
        EC2INSTANCE = 'ec2-user@44.197.215.11'
        APPNAME = 'python-app-demo'
        REGISTRY = 'roxsross12'
        DOCKER_HUB_LOGIN = credentials('dockerhub')
    }

    stages {
        stage('Init') {
            steps {
                echo 'Stage Init'
            }
        }
        stage('Test') {
            steps {
                echo 'Stage Init'
            }
        }

        stage('Build') {
             steps {
                echo 'Stage Build'
                sh 'docker build -t ${APPNAME}:${BUILD_NUMBER} .'
                sh 'docker tag ${APPNAME}:${BUILD_NUMBER} ${REGISTRY}/${APPNAME}:${BUILD_NUMBER}'
                    }
                }
            
        stage('Push to Registry') {
            steps {
                echo 'Stage Push'
                sh 'docker login --username=$DOCKER_HUB_LOGIN_USR --password=$DOCKER_HUB_LOGIN_PSW'
                sh 'docker push ${REGISTRY}/${APPNAME}:${BUILD_NUMBER}'
                echo 'finish'    
            }
        }

        stage('Deploy') {
            steps {
                echo 'Stage Deploy'
                sh ("sed -i -- 's/REGISTRY/$REGISTRY/g' docker-compose.yml")
                sh ("sed -i -- 's/APPNAME/$APPNAME/g' docker-compose.yml")
                sh ("sed -i -- 's/TAG/$BUILD_NUMBER/g' docker-compose.yml")
                sshagent(['ssh-ec2']){
                 sh 'scp -o StrictHostKeyChecking=no docker-compose.yml ${EC2INSTANCE}:/home/ec2-user' 
                 sh 'ssh ${EC2INSTANCE} ls -lrt'
                 sh 'ssh ${EC2INSTANCE} docker-compose up -d'
                }
            }
        }
        stage('Notification') {
            steps {
                echo 'Stage Notify'
                sh 'curl -s -X POST https://api.telegram.org/bot5881753165:AAEjB95ZRDUW0kRMCzMA7C1yjpHemiGTpiM/sendMessage -d chat_id=-1001508340482 -d disable_web_page_preview=True -d text="Deploy Proyecto:$JOB_NAME *$BUILD_NUMBER*" -d parse_mode=markdown'
            }
        }
    }
}

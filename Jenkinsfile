pipeline {
  agent { 
    label 'server'
  }
  
  stages {
    stage('Run on index.html changes') {
      when {
        changeset 'index.html'
      }

      stages {
        stage('Build') {
          steps {
            sh 'docker build -t my-nginx .'
          }
        }

        stage('Run') {
          steps {
            sh 'docker run -d -p 9889:80 --name my-nginx-container my-nginx'
          }
        }

        stage('Test available') {
          steps {
            sh 'curl -s localhost:9889 | grep "An Interesting HTML Page"'
          }
        }

        stage('Compare MD5 Sum') {
          steps {
            sh 'curl -s localhost:9889 | md5sum | grep $(md5sum index.html | awk \'{print $1}\')'
          }
        }
      }

      post {
        always {
          sh 'docker stop my-nginx-container'
          sh 'docker rm my-nginx-container'
        }

        success {
          withCredentials([string(credentialsId: 'bot-token', variable: 'TOKEN'), string(credentialsId: 'chat-id', variable: 'CHAT_ID')]) {
            sh  ("""
                curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='Build success'
            """)
          }
        }

        aborted {
          withCredentials([string(credentialsId: 'bot-token', variable: 'TOKEN'), string(credentialsId: 'chat-id', variable: 'CHAT_ID')]) {
            sh  ("""
                curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='Build aborted'
            """)
          }
        }

        failure {
          withCredentials([string(credentialsId: 'bot-token', variable: 'TOKEN'), string(credentialsId: 'chat-id', variable: 'CHAT_ID')]) {
            sh  ("""
                curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=markdown -d text='Build failed'
            """)
          }
        }
      }
    }
  }
}

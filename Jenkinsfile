pipeline {
  agent { 
    label 'server'
  }
  
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t my-nginx .'
      }
    }
    
    stage('Run') {
      steps {
        sh 'docker run -d -p 80:80 --name my-nginx-container my-nginx'
      }
    }
  }
  
  post {
    always {
      sh 'docker stop my-nginx-container'
      sh 'docker rm my-nginx-container'
    }
  }
}

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
        sh 'docker run -d -p 9889:80 --name my-nginx-container my-nginx'
      }
    }
  }
}

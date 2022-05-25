 pipeline {
  
    agent any
  
    stages {

        stage('Docker Image') {

            agent {
                docker { 
                    image 'docker' 
                    args "--user root --volume /var/run/docker.sock:/var/run/docker.sock"   
                    // Run the container on the node specified at the top-level of the Pipeline, in the same workspace, rather than on a new node entirely:
                    reuseNode true
                }
            }
      
            steps {
                sh 'docker info'
                sh "docker network ls"
                sh 'docker images'
                sh 'docker container list'
                sh "docker ps -a"
                sh "docker ps"
            }
        }
    }
}

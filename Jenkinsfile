pipeline{
    
  agent {
      kubernetes {
        cloud "kubernetes-kind-cloud-2"
        yaml '''
          apiVersion: v1
          kind: Pod
          spec:
            containers:
            - name: node-container
              image: 'node:16.13.1-alpine'
              command:
              - sleep
              args:
              - 99d
          '''
      }
  }
  
  stages{

    stage('NPM Install') {
      
      environment{
        CYPRESS_CACHE_FOLDER = "$WORKSPACE/cypress-cache-override-folder"
      }
      
      steps {
        container('node-container'){
            sh "printenv CYPRESS_CACHE_FOLDER || true"
            echo "${CYPRESS_CACHE_FOLDER}"
            sh "printenv | sort"
            
            sh "whoami"
            sh 'node --version'
            sh "stat -c 'userid - %u, groupid - %g' $PWD"
            sh 'id'
            // this should be PWD=/home/jenkins/agent/workspace/test-trivy-kubernetes
            // but its /home/jenkins ?????
            sh "echo $PWD"
            echo "$PWD"
            // these files will be placed in WORKSPACE
            sh "mkdir testdir"
            sh "touch test.txt"
            sh "ls -a"        
            echo "--------------> Download Repo <---------------"
            git url: 'https://github.com/EdPike365/bmi-calculator.git', branch: 'master'
            //git url: 'https://github.com/jatins/express-hello-world', branch: 'master'
            sh "ls -a"
            echo "--------------> Install Node Modules <---------------"
            // using npm ci instead of npm install, supposed to be faster
            //cypress tries to install files to /root/.cache/...
            //https://github.com/cypress-io/cypress/issues/3081
            //CYPRESS_CACHE_FOLDER=~/Desktop/cypress_cache npm install
            //sh "export CYPRESS_CACHE_FOLDER=$PWD/cypress-cache-override-folder"

            sh "npm install"
            //sh "npm install -g cypress --unsafe-perm --silent"
            script{
                input message: 'proceed?'
            }
        }
      }
   
    }
  }
}

pipeline{
    
  agent {
      kubernetes {
        cloud "kubernetes-kind-cloud-2"
        yaml '''
          apiVersion: v1
          kind: Pod
          spec:
            containers:
            - name: trivy-container
              image: 'aquasec/trivy:0.28.0'
              command:
              - sleep
              args:
              - 99d
          '''
      }
  }
  
  stages{

    stage('Trivy Remote Scan') {
      
      steps {
        container("trivy-container"){
          // we use trivy to scan the image on hub.docker.com
          //sh 'trivy help'
          
          // see https://aquasecurity.github.io/trivy/v0.17.0/usage/image/ for image command options
          // image command: image, -i
          // -f format of output file (assumed)
          // -o output file name
          
          // option 1
          //sh "trivy --cache-dir /tmp image 'edpike365/bmi-calc:1.0'"
          
          // option 2
          // this will not write to the console output, but to a file
          // the "Aquasec Trivy" output will be available at the bottom of the build job output
          sh "trivy --cache-dir /tmp image -f json -o results.json edpike365/bmi-calc:1.0"
          recordIssues(tools: [trivy(pattern: 'results.json')])
        }
      }
    }
  }
}

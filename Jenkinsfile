pipeline {

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '10', numToKeepStr: '3')
    }  

    agent {
        kubernetes {
          cloud "kubernetes-kind-cloud-2"
          yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: kubectl-container
                image: edpike365/kubectl-agent:latest
                command:
                - cat
                tty: true
            '''
        }
    }
    
    stages{
        
        stage("Test JNLP"){
            steps{
                sh 'echo Hello From JNLP'
                sh 'whoami'
                sh 'printenv'
            }
        }
        
        stage("Test KubeCtl Container"){
            steps{
                container('kubectl-container') {
                    sh 'echo Hello from kubectl-container'
                    sh 'whoami'
                    sh 'kubectl version --short'
                }
            }
        }
        
        stage('Create KubeConfig') {

            environment {
                // connect to a cluster
                KUBE_CLUSTER_SERVER_URL = "https://192.168.0.44:58351"
                KUBE_CLUSTER_SECRET_TEXT = credentials('jenkins-kind-3')
        
                // create or use a namespace
                KUBE_NAMESPACE = "my-node-project-ns"
                KUBE_SERVICEACCOUNT = "my-node-project-sa"
            }
            //KUBERNETES_PORT=tcp://10.96.0.1:443
            //KUBERNETES_SERVICE_PORT=443
            steps{
               // script {
                //    env.TAG_ON_DOCKER_HUB = input message: 'User input required', parameters: [choice(name: 'Tag on Docker Hub', choices: 'no\nyes', description: 'Choose "yes" if you want to deploy this build')]
               // }
                
                container('kubectl-container') {
                    echo "--------------- Create local KubeConfig ------------------"
                   // sh 'printenv'
                    sh "env -0 | sort -z | tr '\0' '\n'"
                    //sh "env -0 | sort -z"
                    //sh "env -0"
                    sh 'kubectl config view'
                    sh "kubectl config set-cluster playground --server=${KUBE_CLUSTER_SERVER_URL} --insecure-skip-tls-verify=true || true"
                    sh "kubectl config set-credentials playground-sa --token=${KUBE_CLUSTER_SECRET_TEXT}"
                    sh "kubectl config set-context playground-context --cluster=playground --user=playground-sa"
                    sh "kubectl config use-context playground-context"
                    sh 'kubectl config view'
                    echo "--------------- USE local KubeConfig ------------------"
                    sh "kubectl config set-context --current --namespace=jenkins"
                    sh "kubectl get all -o yaml"
                    // Error from server (Forbidden): pods is forbidden: 
                    //User "system:serviceaccount:jenkins:jenkins" cannot 
                    // list resource "pods" in API group "" at the cluster scope
                }
            }
        }
        // dont forget to clean up with post
    }
      
}

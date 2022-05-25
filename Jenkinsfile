pipeline {

    agent {
        kubernetes {
          cloud "kubernetes-kind-cloud-2"
          yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: kaniko
                image: gcr.io/kaniko-project/executor:debug
                command:
                - sleep
                args:
                - 9999999
                volumeMounts:
                - name: kaniko-secret
                  mountPath: /kaniko/.docker
              restartPolicy: Never
              volumes:
              - name: kaniko-secret
                secret:
                    secretName: kaniko-docker-cred
                    items:
                    - key: .dockerconfigjson
                      path: config.json
            '''
        }
    }
    
    stages{

        stage('Build Image') {
            steps{
                container('kaniko') {
                    echo "hello from kaniko"
                    sh "whoami || true"
                    sh "printenv | sort"
                     sh "ls -a"
                    git url: 'https://github.com/scriptcamp/kubernetes-kaniko.git', branch: 'main'
                    sh "ls -a"
                    echo "PWD is $PWD"
                    echo "WORKSPACE is $WORKSPACE"
                    echo "--------------executing-----------------------------"
                    // WARNING: project name must be file path compatible. No spaces
                    sh '/kaniko/executor --context $WORKSPACE --destination edpike365/hello-kaniko:1.0'
                    //sh '/kaniko/executor --context `pwd` --destination edpike365/hello-kaniko:1.0'
                    //sh '/kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination edpike365/hello-kaniko:1.0'
                    //sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=mydockerregistry:5000/myorg/myimage'
                }
            }
        }
    }
}

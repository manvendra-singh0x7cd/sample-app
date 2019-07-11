pipeline {
  environment {
    DOCKER_REPO="341707006720.dkr.ecr.us-east-1.amazonaws.com/sourcekube/jnlp-slave"
    DOCKE_IMAGE_TAG="latest"
    APPLICATION_REPO="https://github.com/jenkinsci/docker-jnlp-slave.git"
  }
  agent {
    kubernetes {
      defaultContainer 'jnlp'
      label 'Builder'
      yaml """
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: aws-secret
        mountPath: /root/.aws/
      - name: docker-config
        mountPath: /kaniko/.docker
  volumes:
  - name: aws-secret
    secret:
      secretName: aws-secret
  - name: docker-config
    configMap:
      name: docker-config
"""
    }
  }
  stages {
    stage('Build with Kaniko') {
      environment {
        PATH = "/busybox:/kaniko:$PATH"
      }
      steps {
        git "${env.APPLICATION_REPO}"
        container(name: 'kaniko', shell: '/busybox/sh') {
            sh '''#!/busybox/sh
            /kaniko/executor -f `pwd`/Dockerfile -c `pwd`  --cache=true --destination="$DOCKER_REPO":"$DOCKER_IMAGE_TAG"
            '''
        }
      }
    }
  }
}
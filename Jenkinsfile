pipeline {
  agent {
    kubernetes {
      label 'kaniko'
      containerTemplate {
        name 'kaniko'
        image 'gcr.io/kaniko-project/executor:debug'
        command 'sleep'
        args 'infinity'
        ttyEnabled true
        privileged true
      }
      label 'kubectl'
      containerTemplate {
        name 'kubectl'
        image 'bitnami/kubectl:latest'
        command 'sleep'
        args 'infinity'
        ttyEnabled true
        privileged true
      }
    }
  }
  stages {
    stage('Build Dockerfile with Kaniko') {
      agent {
        kubernetes {
          label 'kaniko'
          defaultContainer 'kaniko'
        }
      }
      steps {
        container('kaniko') {
          sh '''
            echo "{\"auths\":{\"$CI_REGISTRY\":{\"auth\":\"$(printf "%s:%s" "$CI_USER" "$CI_PASS" | base64 | tr -d '\n')\"}}}" > /kaniko/.docker/config.json
            /kaniko/executor --context $WORKSPACE \
            --dockerfile docker/Dockerfile \
            --destination husnibakri/laravel-app:latest
          '''
        }
      }
    }
    stage('Deploy k8s yaml manifest') {
      agent {
        kubernetes {
          label 'kubectl'
          defaultContainer 'kubectl'
        }
      }
      steps {
        container('kubectl') {
          withCredentials([kubeconfigFile(credentialsId: 'my-kubeconfig', variable: 'KUBECONFIG')]) {
            sh '''
              kubectl apply -f k8s/deploy.yaml
            '''
          }
        }
      }
    }
  }
}

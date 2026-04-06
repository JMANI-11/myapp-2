pipeline {
  agent any

  
  environment {
    DOCKER_IMAGE = "jmanishankar/argocd-nginx-app"
    TAG = "${BUILD_NUMBER}"
    MANIFEST_REPO = "https://github.com/JMANI-11/k8s-manifests.git"
  }
  
  stages {

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $DOCKER_IMAGE:$TAG .'
      }
    }

    stage('Push Image') {
      steps {
        sh '''
        docker login -u jmanishankar -p dckr_pat_gwhcKYf2PR1lFXe9JILhapY8gzs
        docker push $DOCKER_IMAGE:$TAG
        '''
      }
    }

    stage('Update Manifest Repo') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'github_jakkampudimani18',
          usernameVariable: 'GIT_USER',
          passwordVariable: 'GIT_PASS'
        )]) {

          sh '''
          rm -rf k8s-manifests

          git clone https://$GIT_USER:$GIT_PASS@github.com/JMANI-11/k8s-manifests.git

          cd k8s-manifests

          sed -i "s|image:.*|image: $DOCKER_IMAGE:$TAG|g" deployment.yaml

          git config user.email "jakkampudimani18@gmail.com"
          git config user.name "JMANI-11"

          git add .
          git commit -m "Updated image to $TAG"
          git push
          '''
        }
      }
    }

  }
}

pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "jmanishankar/jenkins-argocd-automatic-nginx-app"
    TAG = "${BUILD_NUMBER}"
    MANIFEST_REPO = "https://github.com/JMANI-11/k8s-manifests-2.git"
  }

  stages {

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $DOCKER_IMAGE:$TAG .'
      }
    }

    stage('Login to DockerHub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
        }
      }
    }

    stage('Push Image') {
      steps {
        sh 'docker push $DOCKER_IMAGE:$TAG'
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
          rm -rf k8s-manifests-2

          git clone https://$GIT_USER:$GIT_PASS@github.com/JMANI-11/k8s-manifests-2.git

          cd k8s-manifests-2

          # Update ONLY nginx container image
          sed -i "s|image: jmanishankar/jenkins-argocd-automatic-nginx-app:.*|image: $DOCKER_IMAGE:$TAG|g" deployment.yaml

          git config user.email "jakkampudimani18@gmail.com"
          git config user.name "JMANI-11"

          git add deployment.yaml
          git commit -m "Update image to $TAG" || echo "No changes"
          git push
          '''
        }
      }
    }
  }
}

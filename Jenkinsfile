pipeline {
  agent any
  stages {
    stage('git scm update') {
      steps {
        git url: 'https://github.com/lampjin/ktcloudinfrajenkins.git', branch: 'main'
      }
    }
   stage('image build and push ') {
        steps {
          sh '''
           #!/bin/bash
            # 에러 발생 시 즉시 스크립트 중단
            set -e
            
            # --- [설정 영역] ---
            DOCKER_USER="lampjin"
            IMAGE_NAME="ktcloudinfra4"
            TAG="0727"
            # ------------------
            
            FULL_IMAGE_NAME="${DOCKER_USER}/${IMAGE_NAME}:${TAG}"
            
            echo "1. Docker 이미지 빌드 시작: ${FULL_IMAGE_NAME}"
            docker build -t "$FULL_IMAGE_NAME" .
            
            echo "2. Docker Hub 푸시 시작: ${FULL_IMAGE_NAME}"
            docker push "$FULL_IMAGE_NAME"
            
            echo "3. 완료되었습니다!"
          '''
        }
      }


    stage('delivery and deployment using k8s') {
      steps {
        sh '''
          ansible all -m ansible.builtin.copy -a "src=/local/deploy.yml dest=/remote/deploy.yml mode=0644" 
          ansible master -m shell -a "kubectl apply -f /remote/deploy.yml"
      '''
      }
    }

  }
}

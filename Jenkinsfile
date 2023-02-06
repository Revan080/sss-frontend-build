pipeline {
  agent {
    kubernetes {
      yamlFile 'build/agent.yaml'
    }
  }

  stages {
        stage('SSS FRONTEND BUILD') {
            steps {
              container('code-builder') {
                sh '''
                    apt install -y libcppunit-dev
                   
                
                    docker build -f Dockerfile -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG --no-cache .
                    
                    docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
              }
                container('image-builder') {
                withCredentials([aws(credentialsId: 'lsacone-dev-sch-ecr', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                sh '''
                   export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                   export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                
                
                   SHA1=`echo $GIT_COMMIT | cut -c1-7`
                   DATE=`date +"%Y-%m-%d-%H%M%S"`
                   TAG=$SHA1-$DATE
                   /kaniko/executor --dockerfile ./pda-sys-admin/pda-sys-ui/Dockerfile --context . --destination=791532114280.dkr.ecr.us-east-1.amazonaws.com/sch-apps:$TAG
                   '''
                }
              } 
              }
        } 

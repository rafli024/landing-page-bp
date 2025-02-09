pipeline {
    agent { label 'kube-masternode' }
    stages {
        stage('Clone Source Code') {
            steps {
                sh '''
                if [ -d landing-page-bp ]
                then
                    cd landing-page-bp && git pull
                else 
                    git clone https://github.com/rafli024/landing-page-bp.git
                fi
                '''
            }
        }
        
        stage('Clone CICD Repo'){
            steps{
                sh '''
                    cd $WORKSPACE && ls -ltr
                '''
            }
        }
        
        stage('Docker Build') {
            steps {
                sh '''
                cd $WORKSPACE/landing-page-bp/landing-page && 
                sudo docker build --no-cache --network=host -t muhammadrafli24/landing-page:${BUILD_NUMBER} -f dockerfile .
                '''
            }
        }
        
        stage('Docker Push'){
            steps{
                withCredentials([string(credentialsId: 'user-docker', variable: 'username'), string(credentialsId: 'docker-passwd', variable: 'password')]) {
                // some block
                sh "sudo docker login -u ${username} -p ${password}"
                    }
                sh '''
                sudo docker push muhammadrafli24/landing-page:${BUILD_NUMBER}
                '''
                }
            }
        
        stage('Deploy to K8S'){
            steps{
                sh'''        
                    kubectl apply -f landing-page-bp/landing-page-yaml              
                    kubectl set image deployment/landing-page landing-page=muhammadrafli24/landing-page:${BUILD_NUMBER}
                    kubectl describe deployments/landing-page
                    kubectl get pods -A
                '''
            }
        }
        stage('CleanUp'){
            steps{
                sh '''
                    sudo docker rmi muhammadrafli24/landing-page:${BUILD_NUMBER}
                '''
            }
        }
    }
}

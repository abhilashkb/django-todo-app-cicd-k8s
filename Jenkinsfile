def getVersion(){
    commitid = sh returnStdout: true, script: '''git rev-parse HEAD'''
    return commitid
}
pipeline {
    agent any
    stages {
        stage('SCM checkout') {
            steps {
                git branch: 'main', credentialsId: '3556331b-958c-41cd-b636-918833670bc2', url: 'https://github.com/abhilashkb/django-todo-app-cicd' 

             script{
	          env.DOCK_TAG = getVersion()
              } 
            }
            }
        stage("Docker Build"){
            steps{
                sh "docker build . -t 224574/django-todo:${DOCK_TAG}"
            }
        }
        stage("Docker Push"){
            steps{
                echo "${DOCK_TAG}"
                withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                sh "docker login -u 224574 -p ${dockerhubpwd}"
                }
                sh "docker push 224574/django-todo:${DOCK_TAG}"
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Write the kubeconfig to a temporary file
                    withCredentials([file(credentialsId: 'my-k8s-config', variable: 'SECRET_FILE')]) {
                    sh 'cat $SECRET_FILE'
                    withEnv(["KUBECONFIG=${SECRET_FILE}"]) {
                        // Run kubectl commands
                        sh 'kubectl get nodes'


            }
        }

            }
        }
    }
}
}
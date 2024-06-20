def getVersion(){
    commitid = sh returnStdout: true, script: '''git rev-parse HEAD'''
    return commitid
}
pipeline {
    agent any
    stages {
        stage('SCM checkout') {
            steps {
                git branch: 'main', credentialsId: '3556331b-958c-41cd-b636-918833670bc2', url: 'https://github.com/abhilashkb/django-todo-app-cicd' {

            }
             script{
	          env.DOCK_TAG = getVersion()
             } 
            }
            }
            }
    }
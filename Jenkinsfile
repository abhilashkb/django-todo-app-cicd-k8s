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
        stage("Deploy Cluster"){
            steps{
            kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJRVBTdXZmaUUwdWN3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TkRBMk1URXhPREF5TXpOYUZ3MHpOREEyTURreE9EQTNNek5hTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURMcDk5RU5DWGNpaHhIdngzZ1FQMDJTd0c2a3VxOUxUR3BCdEt6bVJrNHRNYWdCY0JubjQ0UGNkMTMKeTc5cjJ2VDZrcGZreDc3Q3RGRExmTGNLTTJ2eXBKSkp1OXFFcGRIVnFpWGRLMDE3WWdUaWY4d1FWakpZbEdPdQpNeHJuejZSbHhSdDJyNU9lZ25aMEFxeml2NFBwbU9GeXhNM0xkelFWdWxsSld5aFVEK3NSL3lCdG5BS0cvQUdsCmxBMW5MQ29ZLzgwYlJuVFEwN1laMDk1bFdBWTVMTGE4ekVIcnAzZm5lbGdQc09HcTlqVzJxc0pYcjRlNUZ3enkKc05aTGFBYUpxbGFoNmtXeTQxTjdVZFVzd3NKTWVyU2R4TTEwOFpidm15K0dSSnJBRDJNTjE0ZmJCd29EOVdsbAprTU55SEtJQ3N3MHZhcSt5RnpvZ250dDFTK0NMQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJTcEN6eDBaNmFJT1FjL3BZTFlHcHVSSVJ2djFUQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQlhyRDNmOHBodQpOWHNoQkZGWnQySnNxOW5PNTZYVkxQTC9oL2xKSDBqUnZhOU5aUWNrT3cxa25mbXNmL1Y2ajU1UXJzejdHbTRiCjNzSERKeENsZ1gzbVJOT3htbWxycnJpSmt1TjBzcWFEdkpVbExMcllPL0xSMC9FYjZ4d1FTK1JZajFqRUJVYWIKUmZuUEtaRUtmN2FMeUJmUjRjQVhObzB2ZlFZUzB1UDBqc25YNUdubDRwUGx6TGVraXgxNlo1U3Rma1I4a2dYMwpZZCtUSzNrRncxSHRQWlpDdG1LMmc2a1pSbE9KL2tYakFoQkxJWk45dng5ZWNBeVpoTW9UY0RoUXJlR25CQnZPCkZFT3Zsc21LNXYrZjFOcTRueC9QKzVxZmpVL3lUVjdZaHFwKzN5SFljOGRrY0w5dXM2cG9uaExuc1UwTWRPL2IKRDJzSnJDSm9URFRmCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K', credentialsId: 'jenkinskube', serverUrl: 'https://85.13.217.249:6443') {
         script {
             sh 'kubectl get pods'
                 }
             }
                
  
            }
        }

            }
        }
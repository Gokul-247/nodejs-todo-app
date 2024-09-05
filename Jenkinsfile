pipeline {
    agent any
   

    stages {
        stage('cleanup') {
            steps {
                script {
                    deleteDir()
                }
            }
        }
        stage('git-checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/Akshay-source/nodejs-todo-app.git'
            }
        }

        // stage('Sonar Analysis') {
        //     environment {
        //         scannerHome = tool 'sonar-scanner'
        //     }
        //     steps {
        //         script {
        //         withSonarQubeEnv('sonar') {
        //             sh ''' $scannerHome/bin/sonar-scanner -Dsonar.projectName=nodejs-todo-app \
        //           -Dsonar.sources=. \
        //           -Dsonar.projectKey=nodejs-todo-app '''
        //           }
        //         }
                   
               
        //     }
        // }
        
        stage('OWASP Dependency Check') {
        steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'owasp'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Build'){
            steps{
                        sh 'npm install'
                        sh 'npm pack'
                        sh 'ls -la'
                        

                    }
                
            }
        
        stage('Publish to S3'){
            steps{
                script{
                        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'mycreds']]){
                        // sh 'mkdir node && cp nodejs-todo-1.0.0.tgz node/'
                        // sh 'aws s3 cp node s3://my-bucket-007-todo/ --recursive'
                        sh 'aws s3 cp ./nodejs-todo-1.0.0.tgz s3://my-bucket-007-todo/'
                        }
                }
            }
        }
        
        stage('Deploy to CodeDeploy') {
           steps {
                script {
                        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'mycreds']]) {
                        sh 'aws deploy create-deployment --application-name app01 --deployment-group-name dg01 --s3-location bucket=my-bucket-007-todo,key=nodejs-todo-1.0.0.tgz,bundleType=tgz --region us-east-1'
                    }
                }
            }
        }
    }
}
     

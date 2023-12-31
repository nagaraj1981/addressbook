pipeline {
    agent none
    tools{
        maven 'mymaven'
        jdk 'myjava'
    }

    parameters{
        string(name: 'ENV', defaultValue: 'DEV', description: 'env to compile')
        booleanParam(name: 'executeTest', defaultValue: true, description: 'decide to run tc')
         choice(name: 'APPVERSION', choices: ['1.1', '1.2', '1.3'], description: 'Pick some App Version')
    }
    environment{
        BUILD_SERVER='ec2-user@172.31.3.42'
        IMAGE_NAME='nagaraj1981/dockrepo2018'
        DEPLOY_SERVER='ec2-user@172.31.9.95'
    }

    stages {
        stage('Compile') {
            agent any
             steps {
                script{
            // sshagent(['build-server']){           
                echo "compiling in ${params.ENV} environment"
                sh 'mvn compile'
                

             // }
            }
        }
        }
        stage('UnitTest'){
            agent any
            when{
                expression{
                    params.executeTest == true
                }
            }
            steps{
                script{
               echo "run the unit test cases"
               sh 'mvn test'
               }
            }
            post {
                always {
                  junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Dockerize'){
            agent any
            steps{
                script{
               sshagent(['build-server']){ 
                withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {          
                echo "Containerizing in ${params.ENV} environment"
                //sh 'mvn compile'
                sh "scp -o StrictHostKeyChecking=no server-config.sh ${BUILD_SERVER}:/home/ec2-user"
                sh "ssh -o StrictHostKeyChecking=no ${BUILD_SERVER} 'bash server-config.sh ${IMAGE_NAME} ${BUILD_NUMBER}'"
                sh "ssh ${BUILD_SERVER} sudo docker login -u ${USERNAME} -p ${PASSWORD}"
                sh "ssh ${BUILD_SERVER} sudo docker push ${IMAGE_NAME} ${BUILD_NUMBER}"
                }
            }
            }
            }
        }
        stage("Deploy"){
            agent any
            input{
                message "Select the version to deploy"
                ok "version selecte"
                parameters{
                    choice(name:'NEWAPP', choices:['EKS','OnPrem','EC2'])
                }
            }
            steps{
                script{
                    sshagent(['build-server']){    
                        withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {       
                echo "Deploying in ${params.ENV} environment"
                //sh 'mvn compile'
                
                sh "ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER} sudo yum install docker -y'"
                sh "ssh ${DEPLOY_SERVER} sudo systemctl start docker"
                sh "ssh ${BUILD_SERVER} sudo docker login -u ${USERNAME} -p ${PASSWORD}"
                sh "ssh ${DEPLOY_SERVER} sudo docker run -itd -P ${IMAGE_NAME} ${BUILD_NUMBER}"
                        }

            }
                }
            }
        }
            
    }
}

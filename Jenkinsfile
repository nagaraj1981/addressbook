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
        BUILD_SERVER='ec2-user@172.31.9.95'
    }

    stages {
        stage('Compile') {
            agent any
             steps {
                script{
                   sshagent(['build-server']){           
                echo "compiling in ${params.ENV} environment"
                //sh 'mvn compile'
                sh "scp -o StrictHostKeyChecking=no server-config.sh ${BUILD_SERVER}:/home/ec2-user"
                sh "ssh -o StrictHostKeyChecking=no ${BUILD_SERVER} 'bash server-config.sh'"

            }
            }
        }
        }
        stage('UnitTest'){
            agent{label 'linux_slave'}
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
        stage('Package'){
            agent{label 'linux_slave'}
            steps{
                script{
                echo "Pacakaging the app version ${params.APPVERSION}"
                sh 'mvn package'
            }
            }
        }
        stage("Deploy"){
            input{
                message "Select the version to deploy"
                ok "version selecte"
                parameters{
                    choice(name:'NEWAPP', choices:['EKS','OnPrem','EC2'])
                }
            }
            steps{
                script{
                    echo "Deploy the code"
                    echo "Deploy the app to ${NEWAPP}"
                }
            }
        }
            
    }
}
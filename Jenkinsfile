pipeline {
    agent any
    tools{
        maven 'mymaven'
        jdk 'myjava'
    }

    parameters{
        string(name: 'ENV', defaultValue: 'DEV', description: 'env to compile')
        booleanParam(name: 'executeTest', defaultValue: true, description: 'decide to run tc')
         choice(name: 'APPVERSION', choices: ['1.1', '1.2', '1.3'], description: 'Pick some App Version')
    }

    stages {
        stage('Compile') {
            steps {
                script{
                echo "compiling in ${params.ENV} environment"
                sh 'mvn compile'

            }
            }
        }
        stage('UnitTest'){
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
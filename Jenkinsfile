pipeline {
    agent any

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
                echo "compile the code"

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
               }
            }
        }
        stage('Package'){
            steps{
                script{
                echo "package the code"
                echo "Pacakaging the app version ${params.APPVERSION}"
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
                    echo "Deploy the app to ${params.NEWVERSION}"
                }
            }
        }
            
    }
}
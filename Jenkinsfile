pipeline {
    agent any

    stages {
        stage('Compile') {
            steps {
                script{
                // Get some code from a GitHub repository
                //git 'https://github.com/jglick/simple-maven-project-with-tests.git'

                // Run Maven on a Unix agent.
                //sh "mvn -Dmaven.test.failure.ignore=true clean package"
                echo "compile the code"
                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
            }
        }
        stage('UnitTest'){
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
            }
            }
        }
            
    }
}
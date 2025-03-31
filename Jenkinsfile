pipeline {
    agent any
    
    environment {
        MYSQL_CONTAINER = 'mysql-server'
        MYSQL_ROOT_PASSWORD = 'Welcome@123'
        MYSQL_DATABASE = 'testdb'
    }
    
    stages {
        stage('Pull MySQL Docker Image') {
            steps {
                script {
                    bat 'docker pull docker pull mysql:5.7-oraclelinux7'
                }
            }
        }
        
        stage('Run MySQL Container') {
            steps {
                script {
                    bat """
                    docker run -d --name %MYSQL_CONTAINER% ^
                        --network workshop ^
                        -e MYSQL_ROOT_PASSWORD=%MYSQL_ROOT_PASSWORD% ^
                        -e MYSQL_DATABASE=%MYSQL_DATABASE% ^
                        -p 3306:3306 mysql:5.7-oraclelinux7
                    """
                    
                    // Wait for MySQL to initialize
                    sleep(20)
                }
            }
        }
        
        stage('Copy and Execute SQL Script') {
            steps {
                script {
                    // Assuming test.sql is in Jenkins workspace
                    bat "docker cp testdb.sql %MYSQL_CONTAINER%:/testdb.sql"
                    
                    // Execute the SQL script inside the container
                    bat "docker exec -i %MYSQL_CONTAINER% mysql -uroot -p%MYSQL_ROOT_PASSWORD% %MYSQL_DATABASE% < testdb.sql"
                }
            }
        }
    }
    
    post {
        always {
            script {
                bat "docker logs %MYSQL_CONTAINER%"
            }
        }
        
        success {
            echo 'MySQL Deployment Completed Successfully!'
        }
        
        failure {
            echo 'MySQL Deployment Failed!'
        }
    }
}

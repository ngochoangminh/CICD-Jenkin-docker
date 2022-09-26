pipeline{
    agent any

    options{
        buildDiscarder logRotator (
            daysToKeepStr: '16',
            numToKeepStr: '10'
        )
    }

    evironment {
        NAME='demo-microservice'
        REGISTRY_HOST='0.0.0.0:5000'
        BASE_HREF='/'
    }

    stages {
        state('Deploy latest') {
            when {
                branch 'master'
            }
            evironment{
                REGISTRY_HOST='0.0.0.0:5000'
            }
            steps {
                echo 'building from master'
                 
                sh "./build_docker.sh service_name latest"
                
                # setup on server restful app to pull and run docker compose
                sh "curl -X POST <host>:<port>/pull/<services>"
            }
        }

        stage('Deploy production') {
            when {
                branch 'production'
            }
            environment {
                REGISTRY_HOST='cloud registry host'
            }
            steps {
                echo "Building from production"
                sh "./login_ecr.sh"
                
                sh "./build_dockers.sh service_name prod-latest"
                
            }
        }

        stage('Deploy tag') {
            when {
                tag 'v*.*.*'
            }
            environment {
                REGISTRY_HOST='cloud registry host'
            }
            steps {
                echo "Building from $TAG_NAME"
                sh "./login_ecr.sh"

                sh "./ripts/build_dockers.sh service_name $TAG_NAME"
                
            }
        }
    }

}
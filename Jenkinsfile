pipeline {
    agent none

    stages {
        stage('Get Code') {
            agent{ label 'linux-agent-1' }
            steps {
                //obtener el codigo del repo
                git 'https://github.com/kendor07/helloworld_unir_devops.git'
                sh 'ls'
                sh 'hostname'
            }
        }
        stage('Build') {
            agent{ label 'linux-agent-1' }
            steps {
                echo 'Python code, it does not need compilation'
                echo WORKSPACE
            }
        }
        stage('Test') {
            parallel{
                stage('Unit') {
                    agent{ label 'linux-agent-1' }
                    steps {
                        catchError(buildResult:'UNSTABLE',stageResult:'FAILURE'){
                            sh '''
                                hostname
                                pwd
                                ls
                                export PYTHONPATH=.
                                pytest --junitxml=result-unit.xml test/unit
                            '''
                        }
                    }
                }
                stage('Rest') {
                    agent{ label 'linux-agent-1' }
                    steps {
                        catchError(buildResult:'UNSTABLE',stageResult:'FAILURE'){
                            sh 'hostname'
                            sh 'pwd'
                            sh 'ls'
                            sh ' cp -R ../test3/app/ .'
                            sh ' cp -R ../test3/test/ .'
                            sh 'export FLASK_APP=app/api.py'
                            sh 'flask run --host=0.0.0.0 &'
                            sh 'wget https://repo1.maven.org/maven2/com/github/tomakehurst/wiremock-jre8-standalone/2.35.0/wiremock-jre8-standalone-2.35.0.jar'
                            sh 'java -jar wiremock-jre8-standalone-2.35.0.jar  --port 9090 --root-dir test/wiremock/ &'
                            sh 'pytest --junitxml=result-rest.xml test/rest'
                        }
                    }
                }
            }
        }
        stage('deploy') {
            agent{ label 'linux-agent-3' }
            steps {
                sh 'hostname'
                sh 'pwd'
                sh 'ls'
                sh 'echo I am being run in agent 2'
            }
        }
        stage('Result') {
            agent{ label 'linux-agent-1' }
            steps {
                junit 'result*.xml'
            }
        }
    }
}

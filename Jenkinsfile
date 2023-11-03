pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
                input message: 'Lanjutkan ke tahap Deploy?'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deploy') {
            docker.image('cdrx/pyinstaller-linux:python2').inside {
                try {
                    sh 'pyinstaller --onefile sources/add2vals.py'
                } catch (Exception e) {
                    echo 'Error: ' + e.toString()
                } finally {
                    success {
                        archiveArtifacts 'dist/add2vals'
                        sleep 60
                    }
                }
            }
        }
    }
}
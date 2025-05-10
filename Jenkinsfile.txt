pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\Users\Hazarath\Downloads\apache-jmeter-5.6.3'  // Update this path to your local JMeter installation
        JMETER_TEST_FILE = 'jenkinsfirsttest.jmx'
        RESULT_DIR = 'results'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Git repo will already be checked out by Jenkins, so this step is optional if you configure SCM
                echo "Code checked out from Git"
            }
        }

        stage('Run JMeter Test') {
            steps {
                sh """
                    mkdir -p ${RESULT_DIR}
                    ${JMETER_HOME}/bin/jmeter -n -t ${JMETER_TEST_FILE} -l ${RESULT_DIR}/results.jtl -e -o ${RESULT_DIR}/report
                """
            }
        }

        stage('Archive Results') {
            steps {
                archiveArtifacts artifacts: "${RESULT_DIR}/**", fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}

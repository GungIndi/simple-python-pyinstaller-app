node {
    try {
        stage('Build') {
            // Docker configuration for the Build stage
            def buildContainer = docker.image('python:3.12.1-alpine3.19').inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }

            // Stash the compiled results
            stash name: 'compiled-results', includes: 'sources/*.py*'
        }

        stage('Test') {
            // Docker configuration for the Test stage
            def testContainer = docker.image('qnib/pytest').inside {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }

            // Post-test actionss ni bossssss
            junit 'test-reports/results.xml'
        }
    } catch (Exception e) {
        echo "Pipeline failed: ${e.message}"
        currentBuild.result = 'FAILURE'
    }
}

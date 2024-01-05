node() {
    withDockerContainer('python:3.12.1-alpine3.19') {
        stage('Build') {
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash name: 'compiled-results', includes: 'sources/*.py*'
        }
    }
}
        // stage('Test') {
        //     // Docker configuration for the Test stage
        //     def testContainer = docker.image('qnib/pytest').inside {
        //         sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
        //     }
        //     junit 'test-reports/results.xml'
        // }

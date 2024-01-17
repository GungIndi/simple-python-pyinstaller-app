node() {
    // build block
    withDockerContainer('python:3.12.1-alpine3.19') {
        stage('Build') {
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash name: 'compiled-results', includes: 'sources/*.py*'
        }
    }
    // test block
    withDockerContainer('qnib/pytest') {
        stage('Test') {
            checkout scm
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
    withDockerContainer('python:3.12.1-alpine3.19') {
        stage('Manual Approval')  {         
            checkout scm         
            // Menunggu input persetujuan dari pengguna         
            input message: 'Lanjutkan ke tahap Deploy?', ok: 'Lanjutkan'     
        }
    }
    stage('Deliver'){
        withEnv(['VOLUME=$(pwd)/sources:/src', 'IMAGE=cdrx/pyinstaller-linux:python2', 'VERCEL_SCOPE=gungindi', 'VERCEL_TOKEN=credentials("vercel-token")', 'WORK_DIR=sources/dist/add2vals']){
            dir(path: env.BUILD_ID) { 
                    unstash name: 'compiled-results' 
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                    archiveArtifacts "sources/dist/add2vals"
                    sh "sudo chmod -R -u+rwx \$(pwd)/sources/dist/"
                    sh "sudo chown -R jenkins:jenkins \$(pwd)/sources/dist/"
                    sh "vercel --scope ${VERCEL_SCOPE} --cwd ${WORK_DIR} --no-color --token ${VERCEL_TOKEN} deploy --prebuilt --prod"
                    sleep time: 60
                    cleanWs()
                }
        }
    }
}

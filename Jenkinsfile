
stage('Trivy Security Scan') {
    steps {
        timeout(time: 40, unit: 'MINUTES') {
            bat '''
                docker run --rm ^
                -v trivy-cache:/root/.cache/ ^
                -v /var/run/docker.sock:/var/run/docker.sock ^
                aquasec/trivy:latest image ^
                --timeout 30m ^
                --skip-version-check ^
                --scanners vuln ^
                --severity HIGH,CRITICAL ^
                --exit-code 1 ^
                %DOCKER_IMAGE%:%BUILD_NUMBER%
            '''
        }
    }
}

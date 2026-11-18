
stage('Trivy Security Scan') {
    steps {
        bat '''
            docker run --rm ^
            -v trivy-cache:/root/.cache/ ^
            -v /var/run/docker.sock:/var/run/docker.sock ^
            aquasec/trivy:latest image ^
            --timeout 15m ^
            --scanners vuln ^
            --severity HIGH,CRITICAL ^
            --exit-code 1 ^
            %DOCKER_IMAGE%:%BUILD_NUMBER%
        '''
    }
}
z
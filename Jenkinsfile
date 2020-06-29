pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            agent {
                kubernetes {
                    label 'Test'
                    yaml """
                    kind: Pod
                    metadata:
                      name: docker-daemon
                    spec:
                        containers:
                        - name: docker
                          image: docker:latest
                          command:
                          - cat
                          tty: true
                          securityContext:
                            privileged: true
                          volumeMounts:
                          - name: dind-storage
                            mountPath: /var/run/docker.sock
                          - name: image-storage
                            mountpath: /tmp
                        volumes:
                        - name: dind-storage
                          hostPath:
                            path: /var/run/docker.sock
                        - name: image-storage
                          hostpath:
                            path: /tmp
                        """
                }
            }
            when {
                branch 'master'
            }
            steps {
                 container('docker') {
                    //script {
                        // app = docker.build("sanket07/train-schedule")
                        // app.inside {
                        //     sh 'echo $(curl localhost:8080)'
                        // }
                        // docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
                        //     app.push("${env.BUILD_NUMBER}")
                        //     app.push("latest")
                        // }
                        sh "docker build -t sanket07:$BUILD_NUMBER . -o /tmp"
                    //}
                 }
            }
        }
        
    }
}



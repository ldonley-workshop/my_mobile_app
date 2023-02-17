def branch = env.BRANCH_NAME

pipeline {
    agent {
        kubernetes {
            label "flutter"
            yaml """
apiVersion: v1
kind: Pod
metadata:
  name: flutter
spec:
  containers:
  - name: flutter
    image: cirrusci/flutter:3.7.0
    command:
    - cat
    tty: true
  - name: syft
    image: ldonleycb/syft-runner
    command:
    - cat
    tty: true
    
"""
        }
    }
    options {
        skipDefaultCheckout true
    }
    stages {
        stage('Checkout source code') {
            steps {
                checkout scm
            }
        }
        stage('Build App') {
            steps {
                container('flutter') {
                    sh 'flutter build apk'
                }
            }
        }
        stage('SBOM') {
            steps {
                container('syft') {
                    sh 'syft ./ -o json=sbom.json'
                }
            }
        }
        stage('Archive artifacts') {
            steps {
                archiveArtifacts artifacts: "build/app/outputs/flutter-apk/app-release.apk", fingerprint: true
                archiveArtifacts artifacts: "sbom.json", fingerprint: true
            }
        }
    }
}

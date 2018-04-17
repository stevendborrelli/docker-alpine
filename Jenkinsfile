final def podLabel = "pod-${env.BUILD_TAG}".replace("%2F", "-")

podTemplate(label: podLabel,
    containers: [
        containerTemplate(
            name: 'docker',
            image: 'docker:17.11.0',
            ttyEnabled: true,
            command: 'cat'
        ),
        containerTemplate(
            name: 'gcloud',
            image: 'google/cloud-sdk:196.0.0',
            ttyEnabled: true,
            command: 'cat'
        )
    ],
    volumes: [
        hostPathVolume(
            hostPath: '/var/run/docker.sock',
            mountPath: '/var/run/docker.sock'
        ),
        hostPathVolume(
            hostPath: '/usr/bin/docker',
            mountPath: '/usr/bin/docker',
        )
    ]
) {
    node(podLabel) {
        final scmVars = checkout scm
        final version = "versions/custom-3.7/x86_64/options"
        final imageTag = "gcr.io/${env.GOOGLE_PROJECT}/myalpine:${scmVars.GIT_COMMIT}"

        stage('Build') {
            container('docker') {
                echo 'Building docker image...'
                sh("apk update && apk add bash && ./build ${version}")
            }
        }

        stage('Push') {
            container('gcloud') {
                echo 'Push image to GCR'
                sh("gcloud docker -- push ${imageTag}")
            }
        }
    }
}

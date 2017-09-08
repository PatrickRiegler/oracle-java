properties([
        buildDiscarder(logRotator(numToKeepStr: '5'))
])


buildOracleJavaImages("server-jre")
buildOracleJavaImages("jdk")

def buildOracleJavaImages(String type) {
    def versions
    def registry
    node() {
        sh 'curl --fail --silent --show-error --output jq https://artifactory.six-group.net/artifactory/generic-release/jq/1.5/jq-linux64 && chmod 755 jq'
        versions = findAvailableVersions(type)
        registry = sh returnStdout: true, script: "oc get is oracle-java-${type} --template='{{ .status.dockerImageRepository }}'"
    }
    for (i = 0; i < versions.length; i++) {
        stage("Building ${type} images") {
            node() {
                openshiftBuild bldCfg: 'oracle-java-build', env: [[name: 'SIX_JAVA_VERSION', value: versions[i]], [name: 'SIX_JAVA_PACKAGE', value: type]], showBuildLogs: 'true', verbose: 'false', waitTime: '5', waitUnit: 'min'
                openshiftTag srcStream: "oracle-java-build", srcTag: 'tmp', destStream: "oracle-java-${type}", destTag: versions[i]
                if (i == 0) {
                    openshiftTag srcStream: "oracle-java-build", srcTag: 'tmp', destStream: "oracle-java-${type}", destTag: "latest"
                }
            }
        }
        node('jenkins-slave-image-mgmt') {
            stage("Copy ${type} images") {
                sh "skopeoCopy.sh -f ${registry}:${versions[i]} -t artifactory.six-group.net/sdbi/oracle-java-${type}-8:${versions[i]}"
                if (i == 0) {
                    sh "skopeoCopy.sh -f ${registry}:latest -t artifactory.six-group.net/sdbi/oracle-java-${type}-8:latest"
                }
            }
            stage("Promote ${type} images") {
                withCredentials([string(credentialsId: 'SECRET_ARTIFACTORY_TOKEN', variable: 'ARTIFACTORY_TOKEN')]) {
                    sh "promoteToArtifactory.sh -k ${env.ARTIFACTORY_TOKEN} -i sdbi/oracle-java-${type}-8 -t ${versions[i]} -r sdbi-docker-release-local -c"

                    if (i == 0) {
                        sh "promoteToArtifactory.sh -k ${env.ARTIFACTORY_TOKEN} -i sdbi/oracle-java-${type}-8 -t latest -r sdbi-docker-release-local -c"
                    }
                }
            }
        }
    }
}


def findAvailableVersions(String type) {
    sh 'curl --fail --silent --show-error --output versions.json https://artifactory.six-group.net/artifactory/api/storage/generic-release/oracle/java/8/'
    result = sh returnStdout: true, script: "cat versions.json | ./jq -r '.children | sort_by(.uri) | reverse [] | select(.uri | contains(\"${type}\")) .uri | sub(\"${type}\"; \"\") | split(\"-\")[1]'"
    result.split("\n")
}

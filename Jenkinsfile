// https://github.com/openshift/origin/issues/4198


buildOracleJavaImages("server-jre")
buildOracleJavaImages("jdk")

def buildOracleJavaImages(String type) {
    stage("Building ${type} images") {
        def versions
        def registry
        node() {
            sh 'curl --fail --silent --show-error --output jq https://artifactory.six-group.net/artifactory/generic-release/jq/1.5/jq-linux64 && chmod 755 jq'
            versions = findAvailableVersions(type)
            registry = sh returnStdout: true, script: 'oc get is oracle-java --template=\'{{ .status.dockerImageRepository }}\''
        }
        for (i = 0; i < versions.length; i++) {
            node() {
                openshiftBuild bldCfg: 'oracle-jdk', env: [[name: 'SIX_JAVA_VERSION', value: versions[i]], [name: 'SIX_JAVA_PACKAGE', value: type]], showBuildLogs: 'true', verbose: 'false', waitTime: '5', waitUnit: 'min'
            }
            node('jenkins-slave-image-mgmt') {
                sh "skopeoCopy.sh ${registry}:tmp artifactory.six-group.net/sdbi/oracle-${type}-8:${versions[i]}"
                if (i == 0) {
					sh "skopeoCopy.sh ${registry}:tmp artifactory.six-group.net/sdbi/oracle-${type}-8:latest"
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
FROM artifactory.six-group.net/sdbi/six-rhel7

MAINTAINER HAKA6-Pacemakers <HAKA6-Pacemakers@six-group.com>

ENV JAVA_HOME="/opt/java" \
    SIX_JAVA_VERSION=${SIX_JAVA_VERSION} \
    SIX_JAVA_PACKAGE=${SIX_JAVA_PACKAGE} \
    JAVA_OPTS="-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap"

ENV PATH="${JAVA_HOME}/bin:$PATH"

LABEL six.sdbi.java.version="${SIX_JAVA_PACKAGE}-${SIX_JAVA_VERSION}" \
      six.sdbi.java.securerandom="urandom"

RUN  cd /tmp \
# get base version from java version; substring before '.'
  && export SIX_JAVA_BASE_VERSION=$( echo ${SIX_JAVA_VERSION} | cut -d. -f1) \
  && export VERSION_NAME=${SIX_JAVA_PACKAGE}-${SIX_JAVA_VERSION}u${SIX_JAVA_UPDATE} \
  && curl --fail --silent --show-error --output "jdk.tar.gz" \
     "https://artifactory.six-group.net/artifactory/generic-release/oracle/java/${SIX_JAVA_BASE_VERSION}/${SIX_JAVA_PACKAGE}-${SIX_JAVA_VERSION}_linux-x64_bin.tar.gz" \
  && tar zxf jdk.tar.gz \
  && rm -f jdk.tar.gz \
  && mv /tmp/jdk* /opt \
  && ln -s /opt/jdk*/ $JAVA_HOME \
  && rm /tmp/* -Rf \
  && sed -r -i "s/securerandom\.source=.*/securerandom\.source=file:\/dev\/urandom/g" $JAVA_HOME/conf/security/java.security \
  && sed -r -i "s/crypto\.policy=.*/crypto\.policy=unlimited/g" $JAVA_HOME/conf/security/java.security \
  && fix-permissions.sh /opt/jdk* \
  && fix-permissions.sh $JAVA_HOME

LABEL six.sdbi.java.build="${SIX_BUILD_VERSION}

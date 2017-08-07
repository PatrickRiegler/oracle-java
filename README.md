#Required env variables

- SIX_JAVA_VERSION=8u144
- SIX_JAVA_PACKAGE=server-jre/idk

# Create the server-jre build 144
oc process baseimage-template -v GIT_SOURCE_URL=ssh://git@stash.six-group.net:22/sdbi/oracle-java.git -v BASE_IMAGE_NAME=oracle-server-jre-8u144 -v GIT_CONTEXT_DIR=. -v FROM_IMAGE_STREAM_NAME=six-rhel  | oc create -f -
oc env bc/oracle-server-jre-8u144 SIX_JAVA_VERSION=8u144 SIX_JAVA_PACKAGE=server-jre

# Create the jdk build 144
oc process baseimage-template -v GIT_SOURCE_URL=ssh://git@stash.six-group.net:22/sdbi/oracle-java.git -v BASE_IMAGE_NAME=oracle-jdk-8u144 -v GIT_CONTEXT_DIR=. -v FROM_IMAGE_STREAM_NAME=six-rhel  | oc create -f -
oc env bc/oracle-jdk-8u144 SIX_JAVA_VERSION=8u144 SIX_JAVA_PACKAGE=jdk

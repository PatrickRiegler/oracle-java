#Required env variables


- SIX_JAVA_BASE_VERSION=8
- SIX_JAVA_VERSION=8u131
- SIX_JAVA_PACKAGE=server-jre/idk



# Create the server-jre build
oc process baseimage-template -v GIT_SOURCE_URL=ssh://git@stash.six-group.net:22/sdbi/oracle-java.git -v BASE_IMAGE_NAME=oracle-server-jre -v BASE_IMAGE_TAG=8u131 -v GIT_CONTEXT_DIR=. -v FROM_IMAGE_STREAM_NAME=six-rhel  | oc create -f -
oc env bc/oracle-server-jre-8u131 SIX_JAVA_BASE_VERSION=8 SIX_JAVA_VERSION=8u131 SIX_JAVA_PACKAGE=server-jre

# Create the jdk build
oc process baseimage-template -v GIT_SOURCE_URL=ssh://git@stash.six-group.net:22/sdbi/oracle-java.git -v BASE_IMAGE_NAME=oracle-jdk -v BASE_IMAGE_TAG=8u131 -v GIT_CONTEXT_DIR=. -v FROM_IMAGE_STREAM_NAME=six-rhel  | oc create -f -
oc env bc/oracle-jdk-8u131 SIX_JAVA_BASE_VERSION=8 SIX_JAVA_VERSION=8u131 SIX_JAVA_PACKAGE=jdk

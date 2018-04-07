FROM jboss/base-jdk:7

USER root

COPY opt/ /opt/
COPY local /usr/local

# Modify jboss user
RUN usermod -a -G root jboss && \
	chown  root:root /opt && \
	chmod -R 774 /opt && \
	chown -R jboss:root /opt/webserver && \
	chown -R jboss:root /opt/run-java && \
	chmod -R 774 /opt/webserver && \
	chmod -R 774 /opt/jboss && \
	chmod -R 774 /opt/run-java && \
	chmod 664 /etc/passwd && \
	chown -R jboss:root /usr/local/s2i && \
        chown -R jboss:root /usr/local/dynamic-resources


ENV JWS_HOME="/opt/webserver" JAVA_VERSION="1.7.0" HOME="/opt/jboss" SCRIPT_DEBUG="true" container="oci" \
	JBOSS_PRODUCT="webserver" CATALINA_OPTS="-Djava.security.egd=file:/dev/./urandom" \
	JPDA_ADDRESS="8000" STI_BUILDER="jee"

EXPOSE 8080 8443 8778 9300


# TODO: Put the maintainer name in the image metadata
# MAINTAINER Your Name <your@email.com>

# TODO: Rename the builder environment variable to inform users about application you provide them
# ENV BUILDER_VERSION 1.0

# TODO: Set labels used in OpenShift to describe the builder image
#LABEL io.k8s.description="Platform for building xyz" \
#      io.k8s.display-name="builder x.y.z" \
#      io.openshift.expose-services="8080:http" \
#      io.openshift.tags="builder,x.y.z,etc."

LABEL io.k8s.description="Centos 7 with JWS 3.1 Tomcat 8 and JRE 1.7" \
	com.redhat.deployments-dir="/opt/webserver/webapps" \
	com.redhat.dev-mode.port="JPDA_ADDRESS:8000" \
	org.jboss.product="webserver-tomcat8" \
	org.jboss.product.openjdk.version="1.8.0" \
	org.jboss.product.version="3.1.1" \
	org.jboss.product.webserver-tomcat7.version="3.1.1" \
	io.openshift.s2i.scripts-url="image:///usr/local/s2i" \	
	io.openshift.expose-services="8080:http" \
	version="0.5"

# TODO: Install required packages here:
# RUN yum install -y ... && yum clean all -y

# TODO (optional): Copy the builder files into /opt/app-root
# COPY ./<builder_folder>/ /opt/app-root/

# This default user is created in the openshift/base-centos7 image
#USER 1000
USER 0
# TODO: Set the default CMD for the image
#CMD [ "/opt/webserver/bin/launch.sh"; /bin/bash -c "trap : TERM INT; sleep infinity & wait" ]
#CMD [ "/opt/webserver/bin/launch.sh" ]

# Copyright 2018 Red Hat
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
# ------------------------------------------------------------------------
#
# This is a Dockerfile for the jboss-eap-7-tech-preview/eap-cd-openshift:13.0 image.

FROM jboss-eap-7-tech-preview/eap-cd-openshift:latest

# Environment variables
ENV JBOSS_IMAGE_NAME="jboss-eap-7-tech-preview/eap-cd-openshift" \
    JBOSS_IMAGE_VERSION="13.0" \
    HTTPS_ENABLE_HTTP2="true" \
    JOLOKIA_VERSION="1.5.0" \
    AB_JOLOKIA_PASSWORD_RANDOM="true" \
    AB_JOLOKIA_AUTH_OPENSHIFT="true" \
    AB_JOLOKIA_HTTPS="true" \
    DEFAULT_ADMIN_USERNAME="eapadmin" \
    JBOSS_MODULES_SYSTEM_PKGS="org.jboss.logmanager,jdk.nashorn.api" \
    MAVEN_VERSION="3.5" 

# Labels
LABEL name="$JBOSS_IMAGE_NAME" \
      version="$JBOSS_IMAGE_VERSION" \
      architecture="x86_64"  \
      org.concrt.version="1.4.1" \
      com.redhat.component="jboss-eap-7-eap-cd-openshift-container" \
      io.k8s.description="Platform for building and running JavaEE applications on JBoss EAP continuous delivery" \
      io.k8s.display-name="JBoss EAP continuous delivery" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,javaee,eap,eap7" \
      io.openshift.s2i.scripts-url="image:///usr/local/s2i" \
      description="The JBoss EAP continuous delivery (JBoss EAP CD) releases are intended to be Technology Preview - The JBoss EAP CD OpenShift container image is provided as technology preview. It is intended for development use only. It should NOT be deployed on production or in environments that are not intended for development use." \
      summary="The JBoss EAP continuous delivery (JBoss EAP CD) releases are intended to be Technology Preview - The JBoss EAP CD OpenShift container image is provided as technology preview. It is intended for development use only. It should NOT be deployed on production or in environments that are not intended for development use." \
      io.fabric8.s2i.version.jolokia="1.5.0-redhat-1" \
      io.fabric8.s2i.version.maven="3.5"

# Exposed ports
EXPOSE 8443 8778

USER root

# Add custom repo files
COPY repos/*.repo /etc/yum.repos.d/

# Install required RPMs and ensure that the packages were installed
RUN yum install -y --disablerepo=\* --enablerepo=jboss-rhel-os --enablerepo=jboss-rhel-rhscl python-requests python-enum34 PyYAML hostname rh-maven35 rh-mongodb32-mongo-java-driver postgresql-jdbc mysql-connector-java \
    && yum clean all && \
    rpm -q  python-requests python-enum34 PyYAML hostname rh-maven35 rh-mongodb32-mongo-java-driver postgresql-jdbc mysql-connector-java
# Remove custom repo files
RUN rm /etc/yum.repos.d/jboss-rhel-os.repo /etc/yum.repos.d/jboss-rhel-rhscl.repo

# Add all artifacts to the /tmp/artifacts
# directory
COPY \
    jolokia-jvm-1.5.0.redhat-1-agent.jar \
    openshift-ping-common-1.2.1.Final-redhat-1.jar \
    openshift-ping-dns-1.2.1.Final-redhat-1.jar \
    openshift-ping-kube-1.2.1.Final-redhat-1.jar \
    oauth-20100527.jar \
    rh-sso-7.2.2-eap7-adapter.zip \
    rh-sso-7.2.2-saml-eap7-adapter.zip \
    hawkular-javaagent-1.0.1.Final-redhat-2-shaded.jar \
    /tmp/artifacts/

# Add scripts used to configure the image
COPY modules /tmp/scripts

# Custom scripts
USER root
RUN [ "bash", "-x", "/tmp/scripts/os-java-run/install_as_root" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/dynamic-resources/install.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/s2i-common/install.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/java-alternatives/run.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/os-eap7-openshift/prepare.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap7-openshift/configure.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/os-eap-s2i/prepare.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/os-java-jolokia/install_as_root" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/jboss.eap.cd.jolokia/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/jboss.eap.cd.openshift.modules/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap7-ping/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap-launch/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap7-launch/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap-node-name/configure.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/os-partition/install_as_root" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap-migration/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/jboss.eap.cd.openshift.launch/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/jboss.eap.cd.logging/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap-probes/configure.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/jboss-maven/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap-db-drivers/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap70-sso/configure.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/os-java-hawkular/install_as_root" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/os-eap70-hawkular/install_as_root" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap-hawkular/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap-deployment-scanner/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap-extensions/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/os-eap-sso/configure.sh" ]

USER 185
RUN [ "bash", "-x", "/tmp/scripts/openshift-layer/configure_layers.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/openshift-passwd/configure_passwd.sh" ]

USER root
RUN [ "bash", "-x", "/tmp/scripts/os-logging/configure.sh" ]

USER root
RUN rm -rf /tmp/scripts
USER root
RUN rm -rf /tmp/artifacts

USER 185



CMD ["/opt/eap/bin/openshift-launch.sh"]

LABEL "authoritative-source-url"="registry.access.redhat.com" "distribution-scope"="public" "vendor"="Red Hat, Inc." "url"="https://access.redhat.com/containers/#/registry.access.redhat.com/jboss-eap-7-tech-preview/eap-cd-openshift/images/13.0-3" "vcs-type"="git" "release"="3" "build-date"="2018-06-28T22:35:50.450174" "com.redhat.build-host"="osbs-cpt-005.ocp.osbs.upshift.eng.rdu2.redhat.com" "vcs-ref"="15beea3bfa372e42990cec23797bb481b7054c74"

# Use jbossdemocentral/developer as the base
FROM jbossdemocentral/developer

# Maintainer details
MAINTAINER Andrew Block <andy.block@gmail.com>

# Environment Variables 
ENV BPMS_HOME /opt/jboss/bpms
ENV BPMS_VERSION_MAJOR 6
ENV BPMS_VERSION_MINOR 0
ENV BPMS_VERSION_MICRO 3
ENV BPMS_VERSION_BUILD redhat-1

# ADD Installation Files
COPY support/installation-bpms support/installation-bpms.variables installs/jboss-bpms-installer-$BPMS_VERSION_MAJOR.$BPMS_VERSION_MINOR.$BPMS_VERSION_MICRO.GA-$BPMS_VERSION_BUILD.jar  /opt/jboss/

# Configure project prerequisites and run installer and cleanup installation components
RUN mkdir -p /opt/jboss/bpms-projects \
  && sed -i "s:<installpath>.*</installpath>:<installpath>$BPMS_HOME</installpath>:" /opt/jboss/installation-bpms \
  && java -jar /opt/jboss/jboss-bpms-installer-$BPMS_VERSION_MAJOR.$BPMS_VERSION_MINOR.$BPMS_VERSION_MICRO.GA-$BPMS_VERSION_BUILD.jar  /opt/jboss/installation-bpms -variablefile /opt/jboss/installation-bpms.variables \
  && rm -rf /opt/jboss/jboss-bpms-installer-$BPMS_VERSION_MAJOR.$BPMS_VERSION_MINOR.$BPMS_VERSION_MICRO.GA-$BPMS_VERSION_BUILD.jar /opt/jboss/installation-bpms /opt/jboss/installation-bpms.variables $BPMS_HOME/jboss-eap-6.1/standalone/configuration/standalone_xml_history/

# Copy demo and support files
COPY support/bpm-suite-demo-niogit $BPMS_HOME/jboss-eap-6.1/bin/.niogit
COPY projects /opt/jboss/bpms-projects
COPY support/application-roles.properties support/standalone.xml $BPMS_HOME/jboss-eap-6.1/standalone/configuration/

# Swtich back to root user to perform build and cleanup
USER root

# Run Demo Maven build and cleanup
RUN mvn clean install -f /opt/jboss/bpms-projects/ZipCodeServices/pom.xml \
  && cp -r /opt/jboss/bpms-projects/ZipCodeServices/target/ZipCodeServices-1.0.war $BPMS_HOME/jboss-eap-6.1/standalone/deployments/ \
  && chown -R jboss:jboss $BPMS_HOME/jboss-eap-6.1/bin/.niogit $BPMS_HOME/jboss-eap-6.1/standalone/configuration/application-roles.properties $BPMS_HOME/jboss-eap-6.1/standalone/configuration/standalone.xml $BPMS_HOME/jboss-eap-6.1/standalone/deployments/ZipCodeServices-1.0.war \
  && rm -rf ~/.m2/repository /opt/jboss/bpms-projects  

# Run as JBoss 
USER jboss

# Expose Ports
EXPOSE 9990 9999 8080

# Run BPMS
CMD ["/opt/jboss/bpms/jboss-eap-6.1/bin/standalone.sh","-c","standalone.xml","-b", "0.0.0.0","-bmanagement","0.0.0.0"]

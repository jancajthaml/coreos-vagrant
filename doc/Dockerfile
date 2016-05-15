############################################################
# Dockerfile to build Atlasian Confluence image
############################################################

FROM debian:latest

MAINTAINER piotr.wlodarek@kontomatik.com

ENV CONFLUENCE_VERSION 5.9.10
ENV CONFLUENCE_INSTALLER atlassian-confluence-$CONFLUENCE_VERSION-x64.bin

WORKDIR /root

RUN apt-get update && apt-get install -y curl

# Config file for the installer
RUN echo $'\n\
rmiPort$Long=8000\n\
app.install.service$Boolean=true\n\
existingInstallationDir=/usr/local/Confluence\n\
sys.confirmedUpdateInstallationString=false\n\
sys.languageId=en\n\
sys.installationDir=/opt/atlassian/confluence\n\
app.confHome=/var/atlassian/application-data/confluence\n\
executeLauncherAction$Boolean=true\n\
httpPort$Long=8090\n\
portChoice=custom\n\
' > response.varfile

# Download and run the installer. This should be a single step
# to avoid size explosion due to intermediate layers.
RUN \
  curl -L -O "https://www.atlassian.com/software/confluence/downloads/binary/$CONFLUENCE_INSTALLER" && \
  chmod +x $CONFLUENCE_INSTALLER && \
  ./$CONFLUENCE_INSTALLER -q -varfile response.varfile && \
  rm $CONFLUENCE_INSTALLER

# RUN chown -R confluence:confluence /opt/atlassian/confluence

# Do not run as root
USER confluence

# Prevents this directory from being committed to image
VOLUME /var/atlassian/application-data

EXPOSE 8090

# Start confluence server on container startup
CMD ["/opt/atlassian/confluence/bin/start-confluence.sh", "-fg"]

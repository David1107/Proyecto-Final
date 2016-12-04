FROM jenkins
#Install plugins
RUN /usr/local/bin/install-plugins.sh docker:0.16.2
RUN /usr/local/bin/install-plugins.sh saferestart:0.3
RUN /usr/local/bin/install-plugins.sh git:3.0.1

#setup no run setup wizard
ENV JAVA_OPTS="-Djenkins.install.runSetupWizard=false"
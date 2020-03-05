FROM jenkins/jenkins:lts

USER root

RUN apt-get update

RUN apt-get install -y build-essential

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
RUN unzip awscliv2.zip
RUN ./aws/install

USER jenkins

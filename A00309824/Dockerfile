FROM ubuntu:16.04
MAINTAINER johan.ballesteros@correo.icesi.edu.co

#Configure repository
ADD config/my_key.pub /tmp
RUN apt-key add /tmp/my_key.pub && \
    rm -f /tmp/my_key.pub && \
    echo "deb http://192.168.131.3:8080/ xenial main" > /etc/apt/sources.list && \
    chmod 777 /tmp

#Install packages
RUN apt-get clean && \
    apt-get update -y && \
    apt-get install postgresql -y

EXPOSE 5432

CMD postgresql -m http.server 5432

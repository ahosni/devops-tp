FROM ubuntu/nginx:latest
ARG HOSTNAME=localhost
RUN apt-get update
RUN apt-get install -y openssl
RUN mkdir -p  /etc/nginx/ssl/private \
    mkdir -p  /etc/nginx/ssl/certs
RUN   openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/private/nginx-selfsigned.key \
      -out /etc/nginx/ssl/certs/nginx-selfsigned.crt \
      -subj "/C=TN/ST=Tunis/L=Tunis/O=Global Security/OU=IT Department/CN=${HOSTNAME}"
RUN openssl dhparam -out /etc/nginx/ssl/certs/dhparam.pem 2048
COPY /nginx.conf /etc/nginx/nginx.conf
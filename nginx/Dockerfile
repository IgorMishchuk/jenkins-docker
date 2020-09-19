FROM alpine:latest

LABEL Description="NGINX"

RUN apk update && apk add nginx && mkdir -p /run/nginx && apk add curl

COPY nginx.conf /etc/nginx/

COPY web/ /usr/share/nginx/html/

COPY web/ /var/www/html

EXPOSE 90

ENTRYPOINT ["nginx", "-g", "daemon off;"]

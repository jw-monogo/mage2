FROM alpine:3.8

RUN apk add --update nginx
RUN rm -rf /var/cache/apk/* && rm -rf /tmp/*

RUN rm /etc/nginx/conf.d/default.conf

ADD docker/nginx/nginx.conf /etc/nginx/
ADD docker/nginx/symfony.conf /etc/nginx/conf.d/

RUN adduser -D -g '' -G www-data www-data

RUN mkdir /code
COPY . /code

RUN rm -rf /code/docker && rm -rf Dockerfile && rm -rf Jenkinsfile

CMD ["nginx"]

EXPOSE 80

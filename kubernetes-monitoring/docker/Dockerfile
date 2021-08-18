FROM alpine:latest
RUN apk update && apk add nginx  \
&& mkdir /apt \
&& rm  /etc/nginx/nginx.conf
RUN apk --no-cache add shadow && usermod -u 1001 nginx
COPY nginx.conf /etc/nginx/nginx.conf
COPY homework.html /apt/homework.html
RUN mkdir -p /run/nginx
EXPOSE 80
CMD [ "nginx" ]

FROM nginx:stable

# Create app directory
WORKDIR /app
ADD conf.d /etc/nginx/conf.d
ADD app /app

CMD /usr/sbin/nginx -g 'daemon off;'

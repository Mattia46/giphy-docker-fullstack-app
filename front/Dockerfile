# Dockerfile
FROM node:14 AS builder
COPY . /app
WORKDIR /app

# Installing JQ to manipulate base_url
ENV JQ_VERSION=1.5
RUN wget --no-check-certificate https://github.com/stedolan/jq/releases/download/jq-${JQ_VERSION}/jq-linux64 -O /tmp/jq-linux64
RUN cp /tmp/jq-linux64 /usr/bin/jq
RUN chmod +x /usr/bin/jq

WORKDIR /app/src
RUN contents="$(jq '.BASE_URL = "$BASE_URL"' config.json)" && echo ${contents} > config.json
WORKDIR /app
RUN npm install
RUN npm run build

# Nginx section
FROM nginx:1.17

# copying custom nginx conf
COPY ./nginx.conf /etc/nginx/nginx.conf
RUN mkdir -p /code && chown -R nginx:nginx /code && chmod -R 755 /code

# changing ownership to those folder
RUN chown -R nginx:nginx /var/cache/nginx && \
   chown -R nginx:nginx /var/log/nginx && \
   chown -R nginx:nginx /etc/nginx/conf.d
RUN touch /var/run/nginx.pid && \
   chown -R nginx:nginx /var/run/nginx.pid
RUN chgrp -R root /var/cache/nginx /var/run /var/log/nginx /var/run/nginx.pid && \
   chmod -R 755 /var/cache/nginx /var/run /var/log/nginx /var/run/nginx.pid

# custom nginx script
COPY start_nginx.sh /
RUN chmod +x /start_nginx.sh
USER nginx
WORKDIR /code
COPY --from=builder --chown=nginx /app/dist .
EXPOSE 8080
ENTRYPOINT ["/start_nginx.sh"]

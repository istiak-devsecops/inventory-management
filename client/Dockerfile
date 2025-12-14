# build stage
ARG NODE_VERSION=22
FROM node:${NODE_VERSION} AS builder

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

# runtime stage
ARG NGINX_VERSION=stable-alpine
FROM nginx:${NGINX_VERSION}

# add user
RUN addgroup -S appuser && adduser -S appuser -G appuser

# remove default config and copy custom config
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/nginx.conf

# remove default nginx website 
RUN rm -rf /usr/share/nginx/html/*
COPY --from=builder /app/dist /usr/share/nginx/html
RUN chown -R appuser:appuser /usr/share/nginx/html /var/cache/nginx /var/run

# change permission
RUN chown -R appuser:appuser /var/cache/nginx \
    && chown -R appuser:appuser /var/run \
    && chown -R appuser:appuser /var/log/nginx \
    && chown -R appuser:appuser /usr/share/nginx/html
    
USER appuser

ENV PORT=8080
EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]

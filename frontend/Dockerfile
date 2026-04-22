FROM node:18 AS builder
ARG NODE_ENV=dev
ENV NODE_OPTIONS="--openssl-legacy-provider"
WORKDIR /usr/src/app
COPY ./package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:1.28.2-alpine AS runner
RUN rm -rf /usr/share/nginx/html/*
RUN addgroup -S nginx-group && adduser -S nginx-user -G nginx-group
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /usr/src/app/dist /usr/share/nginx/html/
RUN chown -R nginx-user:nginx-group /var/cache/nginx && \
    chown -R nginx-user:nginx-group /var/log/nginx && \
    chown -R nginx-user:nginx-group /etc/nginx/conf.d && \
    touch /var/run/nginx.pid && \
    chown -R nginx-user:nginx-group /var/run/nginx.pid
USER nginx-user
EXPOSE 80
ENTRYPOINT ["nginx", "-g", "daemon off;"]
HEALTHCHECK --interval=45s --timeout=15s --start-period=15s --retries=5 CMD curl -f http://localhost:80/catalog

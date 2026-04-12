FROM node:18 AS builder
ARG NODE_ENV=dev
ENV NODE_OPTIONS="--openssl-legacy-provider"
WORKDIR /usr/src/app
COPY ./package*.json ./
RUN npm install
COPY . .
RUN npm run build


FROM nginxinc/nginx-unprivileged:stable-alpine AS runner
USER root
RUN rm -rf /usr/share/nginx/html/*
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /usr/src/app/dist /usr/share/nginx/html/
ARG DOCROOT=/usr/share/nginx/html
COPY --chown=nobody:nobody . ${DOCROOT}
RUN find ${DOCROOT} -type d -print0 | xargs -0 chmod 755 && \
    find ${DOCROOT} -type f -print0 | xargs -0 chmod 644 && \
    chmod 755 ${DOCROOT}
USER nginx
EXPOSE 8080
ENTRYPOINT ["nginx", "-g", "daemon off;"]
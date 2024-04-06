FROM node as build
RUN useradd -m app -d /app
USER root

RUN npm install -g corepack && \
    corepack enable
    
USER app

ARG REPO_COMMIT_SHA=main

RUN set -x && \
    git clone https://github.com/OpenDevin/OpenDevin.git /app/src && \
    cd /app/src && \
    git checkout $REPO_COMMIT_SHA && \
    cd /app/src/frontend && \
    corepack install && \
    pnpm install && \
    pnpm install @types/jest && \
    pnpm run make-i18n && \
    pnpm run prepare && \
    ./node_modules/.bin/vite build

FROM nginx
COPY ./nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/src/frontend/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

# Build Stage 1
# This build created a staging docker image
#

FROM node:18-alpine AS appbuild
WORKDIR /usr/src
RUN apk add git; git clone https://github.com/secvisogram/secvisogram.git;cd secvisogram;npm ci;npm run build
RUN ls /usr/src/secvisogram;ls /usr/src/secvisogram/app/dist

# Build Stage 2
# This build takes the production build from staging build
#

FROM nginx:1.23-alpine
COPY --from=appbuild /usr/src/secvisogram/app/dist /usr/share/nginx/html
EXPOSE 80
VOLUME /usr/share/nginx/html/.well-known/appspecific/
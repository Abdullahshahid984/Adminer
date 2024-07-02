# Stage 1: Build
FROM node:16 as build
WORKDIR /usr/src/app
COPY web/package*.json ./
RUN npm install
COPY web/ ./
RUN npm run build

# Stage 2: Serve
FROM nginx:alpine
COPY --from=build /usr/src/app/build /usr/share/nginx/html
COPY web/nginx.conf /etc/nginx/conf.d/default.conf

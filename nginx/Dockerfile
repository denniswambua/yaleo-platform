FROM node:10 as storefront
WORKDIR /app
COPY saleor-storefront/package*.json ./
RUN npm install
COPY saleor-storefront/ .
ARG API_URI
ENV API_URI ${API_URI:-http://localhost:8000/graphql/}
RUN API_URI=${API_URI} npm run build

FROM node:10 as dashboard
WORKDIR /app-dashboard
COPY saleor-dashboard/package*.json ./
RUN npm install
COPY saleor-dashboard .
ARG APP_MOUNT_URI
ARG API_URI
ARG STATIC_URL
ENV API_URI ${API_URI:-http://localhost:8000/graphql/}
ENV APP_MOUNT_URI ${APP_MOUNT_URI:-/dashboard/}
ENV STATIC_URL ${STATIC_URL:-/dashboard/}
RUN STATIC_URL=${STATIC_URL} API_URI=${API_URI} APP_MOUNT_URI=${APP_MOUNT_URI} npm run build

FROM nginx:mainline AS nginx
WORKDIR /app
COPY default.conf /etc/nginx/conf.d/default.conf
COPY --from=storefront /app/dist/ /app
COPY --from=dashboard /app-dashboard/build/ /app/

EXPOSE 80 443

ENTRYPOINT ["nginx", "-g", "daemon off;"]
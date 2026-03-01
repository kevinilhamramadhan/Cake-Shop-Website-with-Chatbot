FROM node:25-alpine as base
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY ..
RUN npm run build

# to-do: configure nginx

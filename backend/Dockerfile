FROM node:25-alpine AS base
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM base as image
WORKDIR /app
RUN apk add -no-cache dumb-init
RUN addgroup -g 1001 -S nodejs && \
    adduser -u 1001 -S nodejs
COPY --from=base /app/node_modules ./node_modules
COPY --chown=nodejs:nodejs ..
USER nodejs
EXPOSE 3000

ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "src/index.js"]

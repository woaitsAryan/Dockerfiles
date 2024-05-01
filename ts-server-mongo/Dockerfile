# Latest LTS as of the time of writing this, will change to 22 in the near future.
FROM node:20-alpine as base

FROM base AS deps

WORKDIR /app
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./
RUN \
    if [ -f yarn.lock ]; then yarn install --frozen-lockfile --production=true; \
    elif [ -f package-lock.json ]; then npm ci --omit=dev; \
    elif [ -f pnpm-lock.yaml ]; then corepack enable pnpm && pnpm i --frozen-lockfile --prod; \
    else echo "Lockfile not found." && exit 1; \
    fi

# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

RUN 

RUN \
    if [ -f yarn.lock ]; then yarn install -D typescript && yarn run build; \
    elif [ -f package-lock.json ]; then npm install -D typescript && npm run build; \
    elif [ -f pnpm-lock.yaml ]; then corepack enable pnpm && pnpm install -D typescript && pnpm run build; \
    else echo "Lockfile not found." && exit 1; \
    fi

FROM base AS runner
WORKDIR /app

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 server_user

RUN mkdir dist
RUN chown server_user:nodejs dist

COPY --from=builder --chown=server_user:nodejs /app/dist ./
COPY --from=builder --chown=server_user:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=server_user:nodejs /app/package.json ./package.json

USER server_user

ENTRYPOINT ["node"]
CMD ["src/index.js"]

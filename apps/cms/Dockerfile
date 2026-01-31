FROM node:20-bookworm-slim AS deps

RUN apt-get update && apt-get install -y \
  build-essential python3 pkg-config \
  libvips-dev \
  && rm -rf /var/lib/apt/lists/*

WORKDIR /srv/app

COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./

RUN if [ -f yarn.lock ]; then \
      corepack enable && yarn install --frozen-lockfile; \
    elif [ -f pnpm-lock.yaml ]; then \
      corepack enable && pnpm install --frozen-lockfile; \
    elif [ -f package-lock.json ]; then \
      npm ci; \
    else \
      npm install; \
    fi

COPY . .

ENV NODE_ENV=production
RUN if [ -f yarn.lock ]; then yarn build; else npm run build; fi

FROM node:20-bookworm-slim AS runner

RUN apt-get update && apt-get install -y \
  libvips \
  && rm -rf /var/lib/apt/lists/*

WORKDIR /srv/app
ENV NODE_ENV=production

COPY --from=deps /srv/app /srv/app

EXPOSE 1337
CMD ["sh", "-lc", "if [ -f yarn.lock ]; then yarn start; else npm run start; fi"]
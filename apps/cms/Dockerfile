# apps/cms/Dockerfile
FROM node:20-bookworm-slim AS deps

RUN apt-get update && apt-get install -y \
  build-essential python3 pkg-config \
  libvips-dev \
  && rm -rf /var/lib/apt/lists/*

WORKDIR /srv/app

# instala deps primeiro (melhor cache)
COPY package.json package-lock.json* yarn.lock* pnpm-lock.yaml* ./

# escolha 1 (npm)
RUN if [ -f package-lock.json ]; then npm ci; else npm install; fi

# copia o resto
COPY . .

# build do admin
ENV NODE_ENV=production
RUN npm run build

# --- runtime ---
FROM node:20-bookworm-slim AS runner

RUN apt-get update && apt-get install -y \
  libvips \
  && rm -rf /var/lib/apt/lists/*

WORKDIR /srv/app
ENV NODE_ENV=production

# copia app pronta
COPY --from=deps /srv/app /srv/app

EXPOSE 1337
CMD ["npm", "run", "start"]
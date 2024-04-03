FROM node:20-alpine AS base

# 1. Install dependencies only when needed
FROM base AS deps
# Check https://github.com/nodejs/docker-node/tree/b4117f9333da4138b03a546ec926ef50a31506c3#nodealpine to understand why libc6-compat might be needed.
RUN apk add --no-cache libc6-compat

WORKDIR /app

# 1. Install dependencies
RUN npm install -g nx

COPY package.json package-lock.json* ./
RUN npm install


# 2. Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules

# Copy configuration files
COPY nx.json .
COPY package.json .
COPY package-lock.json* .
COPY tsconfig.base.json .
COPY .eslintrc.json .
COPY .eslintignore .
COPY .prettierrc .
COPY .prettierignore .
COPY jest.config.ts .
COPY jest.preset.js .

# Include the apps and libs directories
COPY apps/ apps/
COPY dist/ dist/
COPY style-lib/ style-lib/

# Next.js collects completely anonymous telemetry data about general usage.
# Learn more here: https://nextjs.org/telemetry
# Uncomment the following line in case you want to disable telemetry during the build.
ENV NEXT_TELEMETRY_DISABLED 1

# Verify Workspace Files in the Image (temporary)
# After image build run in terminal: docker-compose run <app-name> sh
RUN ls -la

# Build the app using Nx CLI
RUN npx nx build app-1


# 3. Production image, copy all the files and run next
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production
# Uncomment the following line in case you want to disable telemetry during runtime.
ENV NEXT_TELEMETRY_DISABLED 1

# Copy the built application from the previous stage
COPY --from=builder /app/dist/apps/app-1 ./

EXPOSE 3000

# Start the app using Nx CLI
CMD ["npx", "nx", "start", "app-1"]

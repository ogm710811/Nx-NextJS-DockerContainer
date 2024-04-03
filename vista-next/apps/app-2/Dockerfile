# ---- Base Stage ----
# Start with a base node image
FROM node:20-alpine as builder

# Set the working directory in the Docker image
WORKDIR /usr/src/app

# Copy the shared package.json and package-lock.json files
COPY ../package*.json ./

# Install dependencies, including Nx CLI
RUN npm install

# Copy the entire workspace
COPY . ./

# Verify Workspace Files in the Image (temporary)
# After image build run in terminal: docker-compose run <app-name> sh
RUN ls -la

# Build the app using Nx CLI
RUN npx nx build app-2

# ---- Production Stage ----
# Start with a smaller base image for the production stage
FROM node:20-alpine

# Set the working directory in the Docker image
WORKDIR /usr/src/app

# Copy the built application from the previous stage
COPY --from=builder /usr/src/app/dist/apps/app-2 ./

# Expose the port the app runs on
EXPOSE 3001

# Start the app using Nx CLI
CMD ["npx", "nx", "start", "app-2"]

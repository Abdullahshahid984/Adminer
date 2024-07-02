# Use the official Node.js image
FROM node:16

# Set working directory
WORKDIR /usr/src/app

# Set npm registry to Yarn's registry
RUN npm set registry https://registry.yarnpkg.com

# Copy the package.json and package-lock.json
COPY backend/auth/package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code into the container
COPY backend/auth/ .

# Expose the port the app runs on
EXPOSE 3000

# Command to run the application
CMD ["npm", "start"]

# Use a node base image
FROM node:14

# Set the working directory inside the container
WORKDIR /app

# Copy package.json and package-lock.json to the working directory
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy all other source code to the working directory
COPY . .

# Expose the port your app runs on
EXPOSE 4000

# Command to run your application
CMD ["node", "index.js"]



# FROM node:14-alpine3.12
# COPY . .
# EXPOSE 4000
# CMD ["node","index.js"]

FROM node:14

# Create app directory
WORKDIR /app

# Install app dependencies
COPY package*.json ./
RUN npm install

# Bundle app source
COPY . .

# Expose port and define CMD
EXPOSE 4000
CMD [ "node", "index.js" ]



# FROM node:14-alpine3.12
# COPY . .
# EXPOSE 4000
# CMD ["node","index.js"]

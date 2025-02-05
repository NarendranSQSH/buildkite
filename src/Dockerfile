# Use the official Node.js image
FROM node:14

# Create and set the working directory
WORKDIR /usr/src/app

# Copy the package.json and package-lock.json to the working directory
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application
COPY . .

# Expose the port that the app will run on
EXPOSE 8080

# Command to run the application
CMD ["npm", "start"]

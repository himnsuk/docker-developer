# Specify a base image
FROM node:alpine

# Creating Workdir

WORKDIR /usr/app
# Copy files 

COPY ./package.json ./

# Install some dependencies
RUN npm install

COPY ./ ./
# Default command

CMD ["npm", "start"]
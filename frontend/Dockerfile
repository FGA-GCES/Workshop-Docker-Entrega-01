FROM node:14

RUN npm install -g npm@latest

WORKDIR /frontend

COPY ["package*.json", "yarn.lock", "./"]

RUN npm install

COPY . .

CMD ["npm", "run", "dev"]

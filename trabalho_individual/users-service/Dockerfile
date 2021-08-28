FROM node:16

WORKDIR /app

COPY . /app

RUN yarn

COPY . /app

EXPOSE 7101

CMD ["yarn", "watch"]
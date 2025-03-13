FROM node

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 80

ENV MANGO_USERNAME sam
ENV MANGO_PASSWORD secret

CMD [ "npm" ,"start" ]
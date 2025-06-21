FROM node:18

WORKDIR /app

# Instalar las dependencias del proyecto React
RUN npm install

EXPOSE 3000

COPY package*.json ./
RUN npm install

COPY . .

CMD ["npm", "start"]
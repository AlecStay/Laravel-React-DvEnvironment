FROM node:18

WORKDIR /app

# Creamos el proyecto si no existe
RUN [ ! -f "package.json" ] && npx create-react-app . || echo "React app already exists"

EXPOSE 3000

CMD ["npm", "start"]

# build stage
FROM node:22 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

# runtime stage
FROM nginx:stable-alpine

# remove default nginx website first
RUN rm -rf /usr/share/nginx/html/*

COPY --from=builder /app/dist /usr/share/nginx/html

# add user
RUN groupadd -r appuser && useradd -r -g appuser appuser

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

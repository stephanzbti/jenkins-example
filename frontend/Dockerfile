FROM node:11.15.0-alpine

ENV REACT_APP_BACKEND_WS ${REACT_APP_BACKEND_WS}
ENV REACT_APP_BACKEND_URL ${REACT_APP_BACKEND_URL}
ENV NODE_ENV ${NODE_ENV}

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY package.json .
RUN npm install
COPY . .
RUN npm run build

ENTRYPOINT ["npm", "start"]
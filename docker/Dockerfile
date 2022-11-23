FROM node:18-alpine

WORKDIR /app

RUN apk add git g++ make python3 yarn
RUN corepack enable
RUN yarn set version berry
RUN yarn --version
ENV PATH /app/node_modules/.bin:$PATH

ENTRYPOINT [ "yarn" ]
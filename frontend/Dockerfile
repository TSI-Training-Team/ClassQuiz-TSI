# SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)
#
# SPDX-License-Identifier: MPL-2.0

### Build Step
# pull the Node.js Docker image
FROM node:19-bullseye as builder
# ENV API_URL=http://api:80
ENV API_URL=https://classquiz.tsi-training.com
#Ah, I've noticed that!!!
ENV REDIS_URL=redis://localhost:6379
ENV VITE_MAPBOX_ACCESS_TOKEN=p""
# This Mapbox-token is restricted to the following urls: classquiz.de, classquiz.mawoka.eu, test.com
ENV VITE_HCAPTCHA=""
# ENV VITE_SENTRY=https://75cb4ef1be624d8f81bbaf864b722f8a@glitch.mawoka.eu/2
ENV VITE_GOOGLE_AUTH_ENABLED=false
ENV VITE_GITHUB_AUTH_ENABLED=false
ENV VITE_CAPTCHA_ENABLED=true
ENV VITE_REGISTRATION_DISABLED=True
#ENV VITE_PLAUSIBLE_DATA_URL=
# change working directory
WORKDIR /usr/src/app


# copy the package.json files from local machine to the workdir in container
COPY package*.json ./
COPY pnpm-lock.yaml ./

# run npm install in our local machine
RUN corepack enable && corepack prepare pnpm@8.6.1 --activate && pnpm i

# copy the generated modules and all other files to the container
COPY . .

# build the application
RUN pnpm run build

### Serve Step
# pull the Node.js Docker image
FROM node:19-bullseye-slim

# change working directory
WORKDIR /app
COPY --from=builder /usr/src/app/package.json .
COPY --from=builder /usr/src/app/pnpm-lock.yaml .
RUN corepack enable && corepack prepare pnpm@8.6.1 --activate && pnpm i
# copy files from previous step
COPY --from=builder /usr/src/app/build .
COPY --from=builder /usr/src/app/node_modules ./node_modules

# our app is running on port 3000 within the container, so need to expose it
EXPOSE 3000

# the command that starts our app
CMD ["pnpm", "run", "run:prod"]

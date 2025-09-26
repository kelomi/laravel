FROM node:16-bullseye

WORKDIR /usr/src/app

# Install build tools + bash + python2 (for node-gyp/node-sass)
RUN apt-get update && apt-get install -y --no-install-recommends \
    bash \
    python2 \
    python2-dev \
    python3 \
    build-essential \
    g++ \
    make \
    ca-certificates \
    git \
    && rm -rf /var/lib/apt/lists/*

# Tell node-gyp to use python2
ENV PYTHON=/usr/bin/python2

COPY package*.json ./

RUN npm install --no-audit --no-fund

COPY . .

CMD ["npm", "start"]

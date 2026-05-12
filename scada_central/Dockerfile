FROM node:16-bookworm

ARG NODE_SNAP=false

# Packages système
RUN apt-get update && apt-get install -y \
    dos2unix \
    build-essential \
    git \
    unixodbc \
    unixodbc-dev \
    sqlite3 \
    libsqlite3-dev \
    iproute2 \
 && rm -rf /var/lib/apt/lists/*

# Dossier de travail
WORKDIR /usr/src/app

# Clone FUXA
RUN git clone https://github.com/frangoteam/FUXA.git

# Installer drivers ODBC
RUN dos2unix /usr/src/app/FUXA/odbc/install_odbc_drivers.sh && \
    chmod +x /usr/src/app/FUXA/odbc/install_odbc_drivers.sh

WORKDIR /usr/src/app/FUXA/odbc
RUN ./install_odbc_drivers.sh

# Config ODBC
RUN cp /usr/src/app/FUXA/odbc/odbcinst.ini /etc/odbcinst.ini

# Installer FUXA
WORKDIR /usr/src/app/FUXA/server

ENV NODE_OPTIONS=--dns-result-order=ipv4first

RUN npm cache clean --force

RUN npm install --legacy-peer-deps

# Rebuild sqlite3 proprement
RUN npm rebuild sqlite3 --build-from-source

# Snap7 optionnel
RUN if [ "$NODE_SNAP" = "true" ]; then \
      npm install node-snap7; \
    fi

# Entrypoint
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

EXPOSE 1881

ENTRYPOINT ["/entrypoint.sh"]

CMD ["npm", "start"]
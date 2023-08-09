# Bux Console

Bux console is an admin panel that allows for viewing, and in some parts integrating, with bux-server data.

## How to host it

### Locally
1. Clone [bux-console](https://github.com/BuxOrg/bux-console)

2. Create an `env-config.json` file in root of the project and put **bux-server URL** (localhost if you run locally):
```json
{
  "apiUrl": "http://localhost:3003/v1",
}
```

If localhost will not work, try putting http://127.0.0.1:3000/v1 as the url.

3. Run bux-console
```bash
yarn        # to install
yarn dev    # to run
```

4. Enter the url in the browser (usually localhost:3000) and log in with your xPriv

---

### Docker-compose

1. Clone [bux-console](https://github.com/BuxOrg/bux-console)

2. Create an `env-config.json` file in root of the project and put **bux-server URL** (localhost if you run locally):

```json
{
  "apiUrl": "http://localhost:3003/v1",
}
```

3. Add a volume with this file in docker-compose.yml:

```yaml
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - '3000:3000'
    volumes:
      - 'env-config/json:/usr/share/nginx/html/env-config.json'
```

4. Run docker-compose
```bash
docker-compose up -d
```

5. Enter localhost:3000 in your browser and log in with your xPriv

---

## What it offers

1. Admin Dashboard
    * You can quickly jump to useful links from here (like xpubs, transactions, destinations, paymails)
    * Viewing transactions and UTXOS (not implemented yet)
2. Registering an xPub
    * From xPriv
    * Or just by pasting an xPub key
    * Added xPubs are shown in XPubs list
3. Access Keys
    * View access key data
    * Revoke access key (not working - probably a frontend problem)
4. Block headers
    * Viewing block headers (bux-agent required to view these)
5. Destinations
    * Veiwing addresses and locking scripts of destinations
    * Seems to be creating a new destination every minute
6. Paymails
    * Allows for viewing paymails
    * Revoking paymails (not working on local env because of CORS and some other errors)
7. Transactions
    * Viewing transaction data such as timestamp, sender, receiver, hash, etc
    * total_value, fee, output_value seems to be wrong (requires bux-agent)
8. +Transactions
    * Recording a transaction by passing Transaction ID or Hex string
    * Generic error messages, but looks like it's working fine
9. UTXOS
    * Showing spent and unspent UTXOS data
10. XPubs
    * Viewing xpubs

---

## Project structure

* App.js -  entry point with all Providers
* route.js - routes to pages
* .env - env for specifing some titles and bux-server URL
* src
    * components - js components like dashboard, listing, etc
    * hooks - some useful hooks
    * icons - icons used in the dashboard
    * pages - explained [here](#what-it-offers)
    * theme - MUI Themes
    * utils - simple js functions

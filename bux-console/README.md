# Bux Console

Bux console is an admin panel that allows for viewing, and in some parts integrating, with bux-server data.

## How to host it

### Locally
1. Clone [bux-console](https://github.com/BuxOrg/bux-console)
2. Adjust .env file with bux-server URL (localhost if you run locally)
```js
REACT_APP_LOGIN_TITLE="Sign in to a Bux server"
REACT_APP_LOGIN_SUBTITLE="Sign in using your xPriv"
REACT_APP_TRANSPORT_TYPE="http"
REACT_APP_SERVER_URL="http://localhost:3003/v1"  // Remember to put the port 3003 and 'v1' after the URL
REACT_APP_HIDE_SERVER_URL=false
```
If localhost will not work, try putting http://127.0.0.1:3000/v1 as the url

3. Run bux-console
```bash
yarn        # to install
yarn dev    # to run
```

4. Enter the url in the browser (usually localhost:3000) and log in with your xPriv

---

### Docker-compose

1. Clone [bux-console](https://github.com/BuxOrg/bux-console)
2. Adjust docker-compose to put bux-server URL as build-arg
```yaml
version: "3"
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
      args:
        REACT_APP_SERVER_URL: http://localhost:3003/v1 # Bux server URL here
    ports: [3000:3000]
```

1. Run docker-compose
```bash
docker-compose up -d
```

1. Enter localhost:3000 in your browser and log in with your xPriv

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
    * mocks - mock data used for pages that **ARE IMPLEMENTED BUT NOT USED IN CODE**
    * components
        * account - not used
        * customer - not used
        * dashboard - used for admin dashboard - not fully implemented
        * listing 
        * product - not used
        * settings - not used
        * severity-pill - not used
        * other js components - used in the project
    * hooks - some useful hooks
    * icons 
    * pages
        * admin
            * all admin pages explained [here](#what-it-offers)
        * pages that are implemented but not used anywhere:
            * account.js
            * customers.js
            * products.js
            * settings.js
            * register.js
    * theme - MUI Themes
    * utils - simple js functions

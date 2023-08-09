# How to install Bux-related apps locally


## 1. Bux Server

1. Clone [bux-server](https://github.com/BuxOrg/bux-server)
2. Create **xPriv** and **xPub** hd-keys:
    * using [bux-cli](https://github.com/BuxOrg/bux-cli)
    * or using other tools like python or js libraries
    * or using existing wallets (such as e2wallet)

Here's how to do it in Javascript with [bsv library](https://www.npmjs.com/package/bsv):

```javascript
const privKey = bsv.HDPrivateKey.fromRandom()
console.log(privKey.toString())
console.log(privKey.xpubkey.toString())
```

3. Add **xPub** to bux-server config file in *'config/envs/development.json'* as **admin_key**
```json
{
    "debug": true,
        "debug_profiling": false,
        "disable_itc": true,
        "environment": "development",
        "gdpr_compliance": false,
        "request_logging": true,
        "use_mapi_fee_quotes": true,
        "authentication": {
            "admin_key": "xpub661MyMwAqRbcFaYeQLxmExXvTCjw9jjBRpifkoGggkAitXNNjva4TStLJuYjjEmU4AzXRPGwoECjXo3Rgqg8zQqW6UPVfkKtsrogGBw8xz7",
            "require_signing": false,
            "scheme": "xpub",
            "signing_disabled": true
        },
    ...
}
```

4. Run bux-server with development environment variable

```bash
BUX_ENVIRONMENT=development go run ./cmd/server/*
```

5. If you want to support paymail configure your domain using paymail requirements (DNS Records).

*Local ssh tunnel using serveo.net*

1. Start an ssh tunnel on serveo to redirect remote DNS lookups for your domain to your local bux-server
```bash
ssh-keygen -t ed25519 -C "your_email@example.com" # You can use other tools (like Secretive) to generate ssh keys
ssh -i <path/to/generatedKey> -R mySubDomain:80:localhost:3003 serveo.net # Here you put your domain info
```

You can also use *-o ServerAliveInterval=60* to keep the connection from timeouts:
```bash
ssh -o ServerAliveInterval=60 -i <path/to/generatedKey> -R mySubDomain:80:localhost:3003 serveo.net
```

1. Authorize your ssh key by clicking on a link in console from serveo (github or google)

2. Restart the ssh tunnel by the same command:
```bash
ssh -o ServerAliveInterval=60 -i <path/to/generatedKey> -R mySubDomain:80:localhost:3003 serveo.net
```


## Bux-console

1. Clone [bux-console](https://github.com/BuxOrg/bux-console)

2. Create an `env-config.json` file in root of the project and put bux-server URL (localhost if you run locally):
```json
{
  "serverUrl": "http://localhost:3003/v1",
}
```

3. Run bux-console
```bash
yarn        # to install
yarn dev    # to run
```

4. Enter the url in the browser (usually localhost:3000) and log in with your xPriv


## Bux-wallet

1. Clone [bux-wallet-frontend](https://github.com/bitcoin-sv/bux-wallet-frontend)
2. Login to docker and put your access token or password when asked
```bash
docker login --username xyz
```
1. If you want to change the api url, create a file `env-config.json` in root folder:
```js
{
    "apiUrl": "http://localhost:3002"
}
```
1. Create env file with your xpriv generated in the first steps
```bash
echo 'BUX_ADMIN_XPRIV=<<xpriv>>' > bux-wallet-backend.env.private 
```
1. Adjust docker-compose by specifing **bux-server url** and **paymail domain**. If you want to connect to local bux-server, you need to put *host.docker.internal* as host
```yaml
...
  bux-wallet-backend:
    image: bsvb/bux-wallet-frontend:latest
    environment:
      DB_HOST: 'db'
      BUX_SERVER_URL: 'http://host.docker.internal:3003/v1'  # Bux-server address here (remember about port 3003 and 'v1')
      BUX_PAYMAIL_DOMAIN: 'domain.com'
    env_file:
      - bux-wallet-backend.env.private
...
```
1. Exec docker-compose to run backend for wallet
```bash
docker-compose up -d
```
1. Start wallet frontend
```bash
yarn dev
```
**Create a new user and you're ready to accept transactions to your domain.com**

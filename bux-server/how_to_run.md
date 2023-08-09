# How to run it?

<!-- Table of Contents -->
- [How to run it?](#how-to-run-it)
  - [Start server](#start-server)
  - [Databases](#databases)
  - [Paymail](#paymail)
  - [Agents](#agents)
  - [Clients](#clients)

## Start server
To run Bux Server you need to decide which environment you want to use. Without pointing into config file you get this error:
```bash
2023/07/13 12:56:49 type="error" file="config/load.go" method="config.Load" line="44" message="invalid environment variable value: "
2023/07/13 12:56:49 server/main.go:main.main:31 fatal error loading configuration: invalid environment variable value: 
exit status 1
```
> More about configuration files [here](configuration.md)

Let's run it with `development` environment (you can define env in terminal or set it globally):
```bash
 BUX_ENVIRONMENT=development go run ./cmd/server/main.go 
```

If you have debug mode disabled (far fewer logs) you should see something like this:
```bash
2023/07/13 13:10:38 type="info" file="config/load.go" method="config.Load" line="79" message="development configuration env file processed in dir .../bux/bux-server"
2023/07/13 13:10:39 type="error" file="chainstate/client.go" method="chainstate.(*Client).ValidateMiners.func1" line="228" message="No FeeQuote response from miner Taal"
2023/07/13 13:10:41 type="error" file="chainstate/client.go" method="chainstate.(*Client).ValidateMiners.func1" line="228" message="No FeeQuote response from miner Mempool"
2023/07/13 13:10:43 type="error" file="chainstate/client.go" method="chainstate.(*Client).ValidateMiners.func1" line="228" message="No FeeQuote response from miner Matterpool"
2023/07/13 13:10:43 type="debug" file="server/main.go" method="main.main" line="89" message="starting [development] BuxServer server..." port="3003"
```

Bux Server is running on port `3003` and you can access it by `http://localhost:3003` (if you run it locally).
After calling this address you should see this:
```json
{"message":"Welcome to the Bux Server ✌(◕‿-)✌"}
```

## Databases

Bux Server needs to have database connection where Bux engine can store data. Currently, it supports:
1. SQLite
2. PostgreSQL
3. MySQL
4. MongoDB

Database connection, like everything in bux-server, is defined in config [files](configuration.md).

> You can find extended description of Bux database [here](../bux/db/README.md) 

## Paymail

Before starting Bux Server you need to have a [paymail](../../paymail/README.md) domain properly configured.\
Firstly it is necessary to add SRV record to domain which you want to use as paymail domain.
This record will be used for service discovery by Paymail clients - pointing them to your host.

Example of SRV record:
```
Service     _bsvalias
Proto       _tcp
Name        <domain>.<tld>
TTL         3600
Class       IN
Priority    10
Weight      10
Port        443
Target      <endpoint-discovery-host>
```

> More information about setting up SRV record [here](https://bsvalias.org/02-01-host-discovery.html)\
> You can find an example of setting up SRV record in OVH [here](../../paymail/dns_setup/README.md)

After setting up SRV record you need to activate DNSSEC for your domain. DNSSEC, 
short for Domain Name System Security Extensions, is a set of security measures designed to add cryptographic integrity 
to the Domain Name System (DNS). DNSSEC aims to provide authentication and data integrity to DNS responses, 
protecting against various types of attacks such as DNS spoofing and cache poisoning.

> Note: it is possible to use subdomains as paymail domains e.g. `paymail1.bux.com` `paymail2.bux.com` ...

Paymails follow the same format as email addresses {handle}@{domain.tld} e.g. `example@bux.com`. This is used to address a particular user within a particular domain.

## Agents

Bux Server can use a Monitor Agent to listen out for specific transactions on the network.\ 
It is a separate app which is responsible for this service, filtering transactions based on given addresses and sending notifications when appropriate.\
It is necessary to use a Monitor Agent only when you intend to accept payments to bitcoin addresses. Current best practice is to use direct transaction delivery via paymail instead.

> Monitor agent is not required to run Bux Server if you want to use only paymail transactions.

To use monitor you need to deploy it on your server. After that you need to configure Bux Server config e.g. url address and auth token.\
If Monitor is properly configured (you can find whole config section [here](configuration.md)) Bux Server should connect with it at startup.

Main tasks for Monitor:
1. Monitor blockchain
2. Filter transactions based on given addresses (check specific number of days back on startup)
3. Notify Bux Server about new transactions
4. Save new destinations after new transactions

## Clients

To use bux server you need to have at least one client.\

Three options are available:
1. [go-buxclient](../go-buxclient/README.md) - Golang
2. [js-buxclient](../js-buxclient/README.md) - JavaScript
3. [bux-cli](../bux-cli/README.md) - Command Line

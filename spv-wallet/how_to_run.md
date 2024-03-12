# How to run it?

<!-- Table of Contents -->
- [How to run it?](#how-to-run-it)
  - [Start server](#start-server)
  - [Databases](#databases)
  - [Paymail](#paymail)
  - [Clients](#clients)

## Start server

// TODO: -> add more details about how to run it

SPV Wallet is running on port `3003` and you can access it by `http://localhost:3003` (if you run it locally).
After calling this address you should see this:

```json
{"message":"Welcome to the SPV Wallet ✌(◕‿-)✌"}
```

## Databases

SPV Wallet needs to have database connection where SPV Wallet engine can store data. Currently, it supports:

1. SQLite,
2. PostgreSQL,
3. MySQL (experimental),
4. MongoDB (experimental).

Database connection, like everything in SPV Wallet, is defined in config [files](configuration.md).

> You can find extended description of SPV Wallet Engine database [here](./engine/db/README.md)

## Paymail

Before starting SPV Wallet you need to have a [paymail](../../paymail/README.md) domain properly configured.\
At first it is necessary to add SRV record to domain which you want to use as paymail domain.
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

> Note: it is possible to use subdomains as paymail domains e.g. `paymail1.spvwallet.com` `paymail2.spvwallet.com` ...

Paymails follow the same format as email addresses {handle}@{domain.tld} e.g. `example@spvwallet.com`. This is used to address a particular user within a particular domain.

## Clients

To use SPV Wallet you can choose between provided clients.

Three options are available:

1. [spv-wallet-go-client](../spv-wallet-go-client/README.md) - Golang
2. [spv-wallet-js-client](../spv-wallet-js-client/README.md) - JavaScript
3. [spv-wallet-admin-keygen](../spv-wallet-admin-keygen/README.md) - SPV Wallet Admin Keygen

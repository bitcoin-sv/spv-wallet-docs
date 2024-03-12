# GO Bux Client

This is GO library used to communicate with Bux server. It allows us to create an admin or normal user client and
then call methods like create new paymails or generate a new output. 

> Details about all the commands and the repo itself can be found [here](https://github.com/BuxOrg/go-buxclient)

## Installation
```bash
go get -u github.com/BuxOrg/go-buxclient
```

## Create GO project with BuxClient
Initialize project
```bash
go mod init example
```
Add replace lines to `go.mod` file (required as of 05.07.2023)
```bash
replace github.com/dgrijalva/jwt-go => github.com/golang-jwt/jwt/v4 v4.5.0
replace github.com/gomodule/redigo => github.com/gomodule/redigo v1.8.9
replace github.com/centrifugal/centrifuge-go => github.com/centrifugal/centrifuge-go v0.8.3
replace github.com/centrifugal/protocol => github.com/centrifugal/protocol v0.9.1
replace github.com/bsm/redislock => github.com/bsm/redislock v0.7.2
replace github.com/bitcoinschema/go-bob => github.com/bitcoinschema/go-bob v0.2.1
replace go.mongodb.org/mongo-driver => go.mongodb.org/mongo-driver v1.11.7
````
Install go-buxclient
```bash
go get -u github.com/BuxOrg/go-buxclient
```
Create new go file, example `main.go` \
Run app
```bash
go run .
```

## Usage
> Whole script is placed [here](../../../code/js/js-buxclient.js)
* ### Create BuxClient
  To create a BuxClient you need to provide user xpriv, bux-server url. You can also deifine if you want to sign requests or work in debug mode.
  
  * Admin buxclient
    ```go
    adminClient, err := buxclient.New(
      buxclient.WithXPriv("xprv"),
      buxclient.WithAdminKey("xprv"),
      buxclient.WithHTTP("localhost:3003/v1"),
      buxclient.WithDebugging(true),
      buxclient.WithSignRequest(true),
    )
    ```
  * Normal buxclient
    ```go
    userClient, err := buxclient.New(
      buxclient.WithXPriv("xprv"),
      buxclient.WithHTTP("localhost:3003/v1"
      buxclient.WithDebugging(true),
      buxclient.WithSignRequest(true),
	   )
    ```

* ### Register new xPub
  To register new xPub we need to call `RegisterNewXpub` method with new xpub.\
  Additional libraries which will be used in the example:
  * [Bux](github.com/BuxOrg/bux)
  * [go-bk](https://github.com/libsv/go-bk)
  
  Generate new xpub
  ```go
  // Generate entropy
  entropy, err := bip39.GenerateEntropy(160)
  if err != nil {
    panic(err)
  }

  //Generate mnemonic and seed based on entropy
  mnemonic, seed, err := bip39.Mnemonic(entropy, "")

  if err != nil {
    panic(err)
  }

  // Generate xpriv based on seed
  hdXpriv, err := bip32.NewMaster(seed, &chaincfg.MainNet)
  if err != nil {
    panic(err)
  }
  
  // Get xpub from xpriv
  hdXpub, err := hdXpriv.Neuter()
  if err != nil {
    panic(err)
  }
  ```
  Register new xPub in Bux server
  ```bash
  err = adminClient.RegisterXpub(context.Background(), hdXpub.String(), &bux.Metadata{})
  if err != nil {
    panic(err)
  }

* ### Get xPub
  This method returns information about xpub for the client which is calling this method.
  ```go
  xpub, err := userClient.GetXPub(context.Background())
  if err != nil {
    panic(err)
  } 
  ```
  
* ### Create paymail
  Paymail is a new way of creating transaction without knowing the specific address only the paymail. 
  ```go
  // Define alias and domain.
  domain := "bux.com"
  alias := "username"
  
  // Create paymail address.
  address := fmt.Sprintf("%s@%s", alias, domain)
  
  // Register new xpub in BUX.
  err = adminClient.NewPaymail(context.Background(), hdXpub.String(), address, alias, alias, &bux.Metadata{})
  if err != nil {
    panic(err)
  }
  ```

* ### Create new access key
  Access key is another way to autorize user. It can be used like xpub but it is not sufficient to sign transactions.
  ```go
  accessKey, err := userClient.CreateAccessKey(context.Background(), &bux.Metadata{})
  if err != nil {
    panic(err)
  }
  ```

* ### Get Transactions
  This method return all transactions for xpub which was given during bux client creation. You can also filter transactions by providing conditions and use pagination by providing query params.
  ```bash
  conditions := make(map[string]interface{})
  queryParams := datastore.QueryParams{
    Page:          1,
    PageSize:      10,
    OrderByField:  "created_at",
    SortDirection: "desc",
  }
  
  transactions, err := userClient.GetTransactions(context.Background(), conditions, &bux.Metadata{}, &queryParams)
  if err != nil {
    panic(err)
  }

* ### Get Transaction
  This method return specific transaction but only if the transaction is connected with user xpub (transaction is incoming or outgoing for user).
  ```go
  transaction, err := userClient.GetTransaction(context.Background(), "d70a3e6f584ee4e97e4cd1fc2e40f2ab849bdd43e961ebc4af6995ad1fc59287")
  if err != nil {
    panic(err)
  }
  ```

* ### Create transaction
  Transaction can be made in two different ways. First is using `SendToRecipients` method, which include everything inside. Second is by calling every method separately.
  > Making a transaction takes about 20 seconds -  as of 05.07.2023. Proposition to avoid timeout - use goroutines.
    * `SendToRecipients` method\
    It is possible to send metadata which will be saved in transaction object. If you want to send transaction to multiple recipients you need to provide array of recipients. 
  ```go
  // Create recipients
  recipients := []*transports.Recipients{
    {
      Satoshis: 1000,
      To:       recipientPaymail,
    },
  }
  
  // Create matadata with sender and receiver paymails.
  metadata := &bux.Metadata{
    "receiver": receiverPaymail,
    "sender":   senderPaymail,
  }

  // Send transaction.
  transaction, err := c.client.SendToRecipients(context.Background(), recipients, metadata)
  if err != nil {
    panic(err)
  }
  ```
    * Separate methods
  ```go
  // Create draft transaction.
  draftTx, err := userClient.DraftToRecipients(context.Background(), recipients, metadata)
  if err != nil {
    panic(err)
  }

  // Finalize draft transaction.	
  hex, err := userClient.FinalizeTransaction(draftTx)	
  if err != nil {	
    panic(err)	
  }
	
  // Record transaction
  transaction, err = userClient.RecordTransaction(context.Background(), hex, draftTx.ID, metadata)	
  if err != nil {	
    panic(err)	
  }
  ```

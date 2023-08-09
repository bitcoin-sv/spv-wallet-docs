# BUX Configuration

Bux can be configured using special methods described below. They allow functional options to be supplied that overwrite default client options.\
When Bux client is created in [Bux server](../bux-server/README.md) it is configured with default options but by using `WithXXX()` methods you can adjust it to your needs.

In Bux Server file `services.go` you can find the function `loadBux` which is responsible for creating Bux client.\
Inside it, you can find logic which checks the config and adjusts Bux client to it.

Example - adding Paymail support:

```go
    // Set the Paymail server if enabled
    if appConfig.Paymail.Enabled {
	// Append the server config
	options = append(options, bux.WithPaymailSupport(
		appConfig.Paymail.Domains,
		appConfig.Paymail.DefaultFromPaymail,
		appConfig.Paymail.DefaultNote,
		appConfig.Paymail.DomainValidationEnabled,
		appConfig.Paymail.SenderValidationEnabled,
		))
    }
```

## Possible options:

* [General options](#general-options)
* [Cachestore options](#cachestore-options)
* [Datastore options](#datastore-options)
* [Paymail options](#paymail-options)
* [Task Manager options](#task-manager-options)
* [Cluster options](#cluster-options)
* [Notification options](#notification-options)


## General options

<details>
<summary>List of options</summary>

1. [WithUserAgent](#WithUserAgent---will-overwrite-the-default-useragent)
2. [WithNewRelic](#WithNewRelic---will-set-the-NewRelic-application-client)
3. [WithDebugging](#withdebugging---will-set-debugging-in-any-applicable-configuration)
4. [WithEncryptions](#withencryption---will-set-the-encryption-key-and-encrypt-values-using-this-key)
5. [WithModels](#withmodels---will-add-additional-models-will-not-migrate-using-datastore)
6. [WithITCDisabled](#withitcdisabled---will-disable-itc-incoming-transaction-checking)
7. [WithUTCDisabled](#withiucdisabled---will-disable-checking-the-input-utxos)
8. [WithImportBlockHeaders](#withimportblockheaders---will-import-block-headers-on-startup)
9. [WithHTTPClient](#withhttpclient---will-set-the-custom-http-interface)
10. [WithLogger](#withlogger---will-set-the-custom-logger-interface)

</details>


1. ### WithUserAgent -  will overwrite the default useragent
    <details>
    <summary>Show code</summary>
   
    ```go
    func WithUserAgent(userAgent string) ClientOps {
        return func(c *clientOptions) {
            if len(userAgent) > 0 {
                c.userAgent = userAgent
            }
        }
    }
    ```
    </details>

2. ### WithNewRelic - will set the NewRelic application client
   <details>
    <summary>Show code</summary>
   
    ```go
    func WithNewRelic(app *newrelic.Application) ClientOps {
        return func(c *clientOptions) {
            // Disregard if the app is nil
            if app == nil {
            return
            }
            // Set the app
            c.newRelic.app = app
    
            // Enable New relic on other services
            c.cacheStore.options = append(c.cacheStore.options, cachestore.WithNewRelic())
            c.chainstate.options = append(c.chainstate.options, chainstate.WithNewRelic())
            c.dataStore.options = append(c.dataStore.options, datastore.WithNewRelic())
            c.taskManager.options = append(c.taskManager.options, taskmanager.WithNewRelic())
            // c.notifications.options = append(c.notifications.options, notifications.WithNewRelic())
    
            // Enable the service
            c.newRelic.enabled = true
        }
    }
    ```
   </details>

3. ### WithDebugging - will set debugging in any applicable configuration

    <details>
    <summary>Show code</summary>
   
   ```go
    func WithDebugging() ClientOps {
        return func(c *clientOptions) {
            c.debug = true
			
            // Enable debugging on other services
            c.cacheStore.options = append(c.cacheStore.options, cachestore.WithDebugging())
            c.chainstate.options = append(c.chainstate.options, chainstate.WithDebugging()
            c.dataStore.options = append(c.dataStore.options, datastore.WithDebugging())
            c.notifications.options = append(c.notifications.options, notifications.WithDebugging()
            c.taskManager.options = append(c.taskManager.options, taskmanager.WithDebugging())
        }
   }
   ```
    </details>

4. ### WithEncryption - will set the encryption key and encrypt values using this key
    <details>
    <summary>Show code</summary>
   
    ```go
    func WithEncryption(key string) ClientOps {
        return func(c *clientOptions) {
            if len(key) > 0 {
                c.encryptionKey = key
            }
        }
    }
    ```
    </details>
   
5. ### WithModels - will add additional models (will NOT migrate using datastore)
    <details>
    <summary>Show code</summary>
   
    ```go
    func WithModels(models ...interface{}) ClientOps {
        return func(c *clientOptions) {
            c.models = append(c.models, models...)
        }
    }
    ```
    </details>
   
6. ### WithITCDisabled - will disable (ITC) incoming transaction checking
    <details>
    <summary>Show code</summary>
   
    ```go
    func WithITCDisabled() ClientOps {
        return func(c *clientOptions) {
            c.itc = false
        }
    }
    ```
    </details>
   
7. ### WithIUCDisabled - will disable checking the input utxos
    <details>
    <summary>Show code</summary>
   
    ```go
    func WithIUCDisabled() ClientOps {
        return func(c *clientOptions) {
            c.iuc = false
        }
    }
    ```
    </details>

8.  ### WithImportBlockHeaders - will import block headers on startup
    <details>
    <summary>Show code</summary>
   
    ```go
    func WithImportBlockHeaders() ClientOps {
        return func(c *clientOptions) {
            if len(importBlockHeadersURL) > 0 {
                c.importBlockHeadersURL = importBlockHeadersURL
            }
        }
    }
    ```
    </details>

9. ### WithHTTPClient - will set the custom http interface
    <details>
    <summary>Show code</summary>
   
    ```go
    func WithHTTPClient(httpClient *http.Client) ClientOps {
        return func(c *clientOptions) {
            if httpClient != nil {
                c.httpClient = httpClient
            }
        }
    }
    ```
    </details>

10. ### WithLogger - will set the custom logger interface
    <details>
    <summary>Show code</summary>

      ```go
       func WithLogger(customLogger zLogger.GormLoggerInterface) ClientOps {
           return func(c *clientOptions) {
               if customLogger != nil {
                   c.logger = customLogger
   
                   // Enable the logger on all services
                   c.cacheStore.options = append(c.cacheStore.options, cachestore.WithLogger(c.logger))
                   c.chainstate.options = append(c.chainstate.options, chainstate.WithLogger(c.logger))
                   c.dataStore.options = append(c.dataStore.options, datastore.WithLogger(&datastore.DatabaseLogWrapper{GormLoggerInterface: c.logger}))
                   c.taskManager.options = append(c.taskManager.options, taskmanager.WithLogger(c.logger))
                   c.notifications.options = append(c.notifications.options, notifications.WithLogger(c.logger))
               }
           }
       }    
      ```
   </details>

## Cachestore options

<details>
<summary>List of options</summary>

1. [WithCustomCachestore](#withcustomcachestore---will-set-the-cachestore)
2. [WithFreeCache](#withfreecache---will-set-the-cache-client-for-both-read--write-clients)
3. [WithFreeCacheConnection](#withfreecacheconnection---will-set-the-cache-client-to-an-active-freecache-connection)
4. [WithRedis](#withredis---will-set-the-redis-cache-client-for-both-read--write-clients)
5. [WithRedisConnection](#withredis---will-set-the-redis-cache-client-for-both-read--write-clients)

</details>

1. ### WithCustomCachestore - will set the cachestore
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithCustomCachestore(cacheStore cachestore.ClientInterface) ClientOps {
        return func(c *clientOptions) {
            if cacheStore != nil {
                c.cacheStore.ClientInterface = cacheStore
            }
        }
    }
    ```
    </details>

2. ### WithFreeCache - will set the cache client for both Read & Write clients
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithFreeCache() ClientOps {
        return func(c *clientOptions) {
            c.cacheStore.options = append(c.cacheStore.options, cachestore.WithFreeCache())
        }
    }
    ```
    </details>

3. ### WithFreeCacheConnection - will set the cache client to an active FreeCache connection
   <details>
    <summary>Show code</summary>
    
    ```go
    func WithFreeCacheConnection(client *freecache.Cache) ClientOps {
        return func(c *clientOptions) {
            if client != nil {
                c.cacheStore.options = append(
                    c.cacheStore.options,
                    cachestore.WithFreeCacheConnection(client),
                )
            }
        }
    }
    ```
    </details>

4. ### WithRedis - will set the redis cache client for both Read & Write clients
   <details>
    <summary>Show code</summary>
    
    ```go
    // This will load new redis connections using the given parameters
    func WithRedis(config *cachestore.RedisConfig) ClientOps {
        return func(c *clientOptions) {
            if config != nil {
                c.cacheStore.options = append(c.cacheStore.options, cachestore.WithRedis(config))
            }
        }
    }
    ```
    </details>

5. ### WithRedisConnection - will set the cache client to an active redis connection
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithRedisConnection(activeClient *cache.Client) ClientOps {
        return func(c *clientOptions) {
            if activeClient != nil {
                c.cacheStore.options = append(
                    c.cacheStore.options,
                    cachestore.WithRedisConnection(activeClient),
                )
            }
        }
    }
    ```
    </details>

## Datastore options

<details>
<summary>List of options</summary>

1. [WithCustomDatastore](#withcustomdatastore---will-set-the-datastore)
2. [WithAutoMigrate](#withautomigrate---will-enable-auto-migrate-database-mode-given-models)
3. [WithMigrationDisabled](#withmigrationdisabled---will-disable-all-migrations-from-running-in-the-datastore)
4. [WithSQLite](#withsqlite---will-set-the-datastore-to-use-sqlite)
5. [WithSQL](#withsql---will-set-the-datastore-to-use-the-sql-config)
6. [WithSQLConfigs](#withsqlconfigs---will-load-multiple-connections-replica--master)
7. [WithSQLConnection](#withsqlconnection---will-set-the-datastore-to-an-existing-connection-for-mysql-or-postgresql)
8. [WithMongoDB](#withmongodb---will-set-the-datastore-to-use-mongodb)
9. [WithMongoConnection](#withmongoconnection---will-set-the-datastore-to-an-existing-connection-for-mongodb)

</details>

1. ### WithCustomDatastore - will set the datastore
   <details>
    <summary>Show code</summary>
    
    ```go
    func WithCustomDatastore(dataStore datastore.ClientInterface) ClientOps {
        return func(c *clientOptions) {
            if dataStore != nil {
                c.dataStore.ClientInterface = dataStore
            }
        }
    }
    ```
    </details>

2. ### WithAutoMigrate - will enable auto migrate database mode (given models)
    <details>
    <summary>Show code</summary>
    
    ```go
    // Pointers of structs (IE: &models.Xpub{})
    func WithAutoMigrate(migrateModels ...interface{}) ClientOps {
        return func(c *clientOptions) {
            if len(migrateModels) > 0 {
                c.addModels(modelList, migrateModels...)
                c.addModels(migrateList, migrateModels...)
            }
        }
    }
    ```
    </details>

3. ### WithMigrationDisabled - will disable all migrations from running in the Datastore
   <details>
    <summary>Show code</summary>
    
    ```go
    func WithMigrationDisabled() ClientOps {
        return func(c *clientOptions) {
            c.dataStore.migrationDisabled = true
        }
    }
    ```
    </details>

4. ### WithSQLite - will set the Datastore to use SQLite
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithSQLite(config *datastore.SQLiteConfig) ClientOps {
        return func(c *clientOptions) {
            if config != nil {
                c.dataStore.options = append(c.dataStore.options, datastore.WithSQLite(config))
            }
        }
    }
    ```
    </details>

5. ### WithSQL - will set the datastore to use the SQL config
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithSQL(engine datastore.Engine, config *datastore.SQLConfig) ClientOps {
        return func(c *clientOptions) {
            if config != nil && !engine.IsEmpty() {
                c.dataStore.options = append(
                    c.dataStore.options,
                    datastore.WithSQL(engine, []*datastore.SQLConfig{config}),
                )
            }
        }
    }
    ```
    </details>

6. ### WithSQLConfigs - will load multiple connections (replica & master)
   <details>
    <summary>Show code</summary>
    
    ```go
    func WithSQLConfigs(engine datastore.Engine, configs []*datastore.SQLConfig) ClientOps {
        return func(c *clientOptions) {
            if len(configs) > 0 && !engine.IsEmpty() {
                c.dataStore.options = append(
                    c.dataStore.options,
                    datastore.WithSQL(engine, configs),
                )
            }
        }
    }
    ```
    </details>

7. ### WithSQLConnection - will set the Datastore to an existing connection for MySQL or PostgreSQL
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithSQLConnection(engine datastore.Engine, sqlDB *sql.DB, tablePrefix string) ClientOps {
        return func(c *clientOptions) {
            if sqlDB != nil && !engine.IsEmpty() {
                c.dataStore.options = append(
                    c.dataStore.options,
                    datastore.WithSQLConnection(engine, sqlDB, tablePrefix),
                )
            }
        }
    }
    ```
    </details>

8. ### WithMongoDB - will set the Datastore to use MongoDB
   <details>
    <summary>Show code</summary>
    
    ```go
    func WithMongoDB(config *datastore.MongoDBConfig) ClientOps {
        return func(c *clientOptions) {
            if config != nil {
                c.dataStore.options = append(c.dataStore.options, datastore.WithMongo(config))
            }
        }
    }
    ```
    </details>

9. ### WithMongoConnection - will set the Datastore to an existing connection for MongoDB
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithMongoConnection(database *mongo.Database, tablePrefix string) ClientOps {
        return func(c *clientOptions) {
            if database != nil {
                c.dataStore.options = append(
                    c.dataStore.options,
                    datastore.WithMongoConnection(database, tablePrefix),
                )
            }
        }
    }
    ```
    </details>

## Paymail options

<details>
<summary>List of options</summary>

1. [WithPaymailClient](#withpaymailclient---will-set-a-custom-paymail-client)
2. [WithPaymailSupport](#withpaymailsupport---will-set-the-configuration-for-paymail-support-as-a-server)
3. [WithPaymailServerConfig](#withpaymailserverconfig---will-set-the-custom-server-configuration-for-paymail)

</details>

1. ### WithPaymailClient - will set a custom paymail client
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithPaymailClient(client paymail.ClientInterface) ClientOps {
        return func(c *clientOptions) {
            if client != nil {
                c.paymail.client = client
            }
        }
    }
    ```
    </details>

2. ### WithPaymailSupport - will set the configuration for Paymail support (as a server)
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithPaymailSupport(domains []string, defaultFromPaymail, defaultNote string,
        domainValidation, senderValidation bool,
    ) ClientOps {
        return func(c *clientOptions) {
            // Add generic capabilities
            c.paymail.serverConfig.options = append(c.paymail.serverConfig.options, server.WithP2PCapabilities())

            // Add each domain
            for _, domain := range domains {
                c.paymail.serverConfig.options = append(c.paymail.serverConfig.options, server.WithDomain(domain))
            }

            // Set the sender validation
            if senderValidation {
                c.paymail.serverConfig.options = append(c.paymail.serverConfig.options, server.WithSenderValidation())
            }

            // Domain validation
            if !domainValidation {
                c.paymail.serverConfig.options = append(c.paymail.serverConfig.options, server.WithDomainValidationDisabled())
            }

            // Add default values
            if len(defaultFromPaymail) > 0 {
                c.paymail.serverConfig.DefaultFromPaymail = defaultFromPaymail
            }
            if len(defaultNote) > 0 {
                c.paymail.serverConfig.DefaultNote = defaultNote
            }

            // Add the paymail_address model in bux
            c.addModels(migrateList, newPaymail(""))
        }
    }
    ```
    </details>

3. ### WithPaymailServerConfig - will set the custom server configuration for Paymail
    <details>
    <summary>Show code</summary>
    
    ```go
    // This will allow overriding the Configuration.actions (paymail service provider)
    func WithPaymailServerConfig(config *server.Configuration, defaultFromPaymail, defaultNote string) ClientOps {
        return func(c *clientOptions) {
            if config != nil {
                c.paymail.serverConfig.Configuration = config
            }
            if len(defaultFromPaymail) > 0 {
                c.paymail.serverConfig.DefaultFromPaymail = defaultFromPaymail
            }
            if len(defaultNote) > 0 {
                c.paymail.serverConfig.DefaultNote = defaultNote
            }

            // Add the paymail_address model in bux
            c.addModels(migrateList, newPaymail(""))
        }
    }
    ```
    </details>

## Task Manager options


<details>
<summary>List of options</summary>

1. [WithCustomTaskManager](#withcustomtaskmanager---will-set-the-taskmanager)
2. [WithTaskQ](#withtaskq---will-set-the-task-manager-to-use-taskq--in-memory)
3. [WithTaskQUsingRedis](#withtaskqusingredis---will-set-the-task-manager-to-use-taskq--redis)
4. [WithCronService](#withcronservice---will-set-the-custom-cron-service-provider)

</details>

1. ### WithCustomTaskManager - will set the taskmanager
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithCustomTaskManager(taskManager taskmanager.ClientInterface) ClientOps {
        return func(c *clientOptions) {
            if taskManager != nil {
                c.taskManager.ClientInterface = taskManager
            }
        }
    }
    ```
    </details>

2. ### WithTaskQ - will set the task manager to use TaskQ & in-memory
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithTaskQ(config *taskq.QueueOptions, factory taskmanager.Factory) ClientOps {
        return func(c *clientOptions) {
            if config != nil {
                c.taskManager.options = append(
                    c.taskManager.options,
                    taskmanager.WithTaskQ(config, factory),
                )
            }
        }
    }
    ```
    </details>

3. ### WithTaskQUsingRedis - will set the task manager to use TaskQ & Redis
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithTaskQUsingRedis(config *taskq.QueueOptions, redisOptions *redis.Options) ClientOps {
        return func(c *clientOptions) {
            if config != nil {

                // Create a new redis client
                if config.Redis == nil {

                    // Remove prefix if found
                    redisOptions.Addr = strings.Replace(redisOptions.Addr, cachestore.RedisPrefix, "", -1)
                    config.Redis = redis.NewClient(redisOptions)
                }

                c.taskManager.options = append(
                    c.taskManager.options,
                    taskmanager.WithTaskQ(config, taskmanager.FactoryRedis),
                )
            }
        }
    }
    ```
    </details>

4. ### WithCronService - will set the custom cron service provider
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithCronService(cronService taskmanager.CronService) ClientOps {
        return func(c *clientOptions) {
            if cronService != nil && c.taskManager != nil {
                c.taskManager.options = append(c.taskManager.options, taskmanager.WithCronService(cronService))
            }
        }
    }
    ```
    </details>

## Cluster options


<details>
<summary>List of options</summary>

1. [WithClusterRedis](#withclusterredis---will-set-the-cluster-coordinator-to-use-redis)
2. [WithClusterKeyPrefix](#withclusterkeyprefix---will-set-the-cluster-key-prefix-to-use-for-all-keys-in-the-cluster-coordinator)
3. [WithClusterClient](#withclusterclient---will-set-the-cluster-options-on-the-client)

</details>

1. ### WithClusterRedis - will set the cluster coordinator to use redis
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithClusterRedis(redisOptions *redis.Options) ClientOps {
        return func(c *clientOptions) {
            if redisOptions != nil {
                c.cluster.options = append(c.cluster.options, cluster.WithRedis(redisOptions))
            }
        }
    }
    ```
    </details>

2. ### WithClusterKeyPrefix - will set the cluster key prefix to use for all keys in the cluster coordinator
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithClusterKeyPrefix(prefix string) ClientOps {
        return func(c *clientOptions) {
            if prefix != "" {
                c.cluster.options = append(c.cluster.options, cluster.WithKeyPrefix(prefix))
            }
        }
    }
    ```
    </details>

3. ### WithClusterClient - will set the cluster options on the client
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithClusterClient(clusterClient cluster.ClientInterface) ClientOps {
        return func(c *clientOptions) {
            if clusterClient != nil {
                c.cluster.ClientInterface = clusterClient
            }
        }
    }
    ```
    </details>

## Chain State options

<details>
<summary>List of options</summary>

1. [WithCustomChainstate](#withcustomchainstate---will-set-the-chainstate)
2. [WithChainstateOptions](#withchainstateoptions---will-set-chainstate-defaults)
3. [WithBroadcastMiners](#withbroadcastminers---will-set-a-list-of-miners-for-broadcasting)
4. [WithQueryMiners](#withqueryminers---will-set-a-list-of-miners-for-querying-transactions)
5. [WithWhatsOnChainAPIKey](#withwhatsonchainapikey---will-set-the-api-key)
6. [WithNowNodesAPIKey](#withnownodesapikey---will-set-the-api-key)
7. [WithExcludedProviders](#withexcludedproviders---will-set-a-list-of-excluded-providers)
8. [WithMonitoring](#withmonitoring---will-create-a-new-monitorconfig-interface-with-the-given-options)
9. [WithMonitoringInterface](#withmonitoringinterface---will-set-the-interface-to-use-for-monitoring-the-blockchain)

</details>

1. ### WithCustomChainstate - will set the chainstate
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithCustomChainstate(chainState chainstate.ClientInterface) ClientOps {
        return func(c *clientOptions) {
            if chainState != nil {
                c.chainstate.ClientInterface = chainState
            }
        }
    }
    ```
    </details>

2. ### WithChainstateOptions - will set chainstate defaults
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithChainstateOptions(broadcasting, broadcastInstant, paymailP2P, syncOnChain bool) ClientOps {
        return func(c *clientOptions) {
            c.chainstate.broadcasting = broadcasting
            c.chainstate.broadcastInstant = broadcastInstant
            c.chainstate.paymailP2P = paymailP2P
            c.chainstate.syncOnChain = syncOnChain
        }
    }
    ```
    </details>

3. ### WithBroadcastMiners - will set a list of miners for broadcasting
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithBroadcastMiners(miners []*chainstate.Miner) ClientOps {
        return func(c *clientOptions) {
            if len(miners) > 0 {
                c.chainstate.options = append(c.chainstate.options, chainstate.WithBroadcastMiners(miners))
            }
        }
    }
    ```
    </details>

4. ### WithQueryMiners - will set a list of miners for querying transactions
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithQueryMiners(miners []*chainstate.Miner) ClientOps {
        return func(c *clientOptions) {
            if len(miners) > 0 {
                c.chainstate.options = append(c.chainstate.options, chainstate.WithQueryMiners(miners))
            }
        }
    }
    ```
    </details>

5. ### WithWhatsOnChainAPIKey - will set the API key
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithWhatsOnChainAPIKey(apiKey string) ClientOps {
        return func(c *clientOptions) {
            if len(apiKey) > 0 {
                c.chainstate.options = append(c.chainstate.options, chainstate.WithWhatsOnChainAPIKey(apiKey))
            }
        }
    }
    ```
    </details>

6. ### WithNowNodesAPIKey - will set the API key
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithNowNodesAPIKey(apiKey string) ClientOps {
        return func(c *clientOptions) {
            if len(apiKey) > 0 {
                c.chainstate.options = append(c.chainstate.options, chainstate.WithNowNodesAPIKey(apiKey))
            }
        }
    }
    ```
    </details>

7. ### WithExcludedProviders - will set a list of excluded providers
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithExcludedProviders(providers []string) ClientOps {
        return func(c *clientOptions) {
            if len(providers) > 0 {
                c.chainstate.options = append(c.chainstate.options, chainstate.WithExcludedProviders(providers))
            }
        }
    }
    ```
    </details>

8. ### WithMonitoring - will create a new monitorConfig interface with the given options
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithMonitoring(ctx context.Context, monitorOptions *chainstate.MonitorOptions) ClientOps {
        return func(c *clientOptions) {
            if monitorOptions != nil {
                c.chainstate.options = append(c.chainstate.options, chainstate.WithMonitoring(ctx, monitorOptions))
            }
        }
    }
    ```
    </details>

9.  ### WithMonitoringInterface - will set the interface to use for monitoring the blockchain
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithMonitoringInterface(monitor chainstate.MonitorService) ClientOps {
        return func(c *clientOptions) {
            if monitor != nil {
                c.chainstate.options = append(c.chainstate.options, chainstate.WithMonitoringInterface(monitor))
            }
        }
    }
    ```
    </details>

## Notification options

<details>
<summary>List of options</summary>

1. [WithNotifications](#withnotifications---will-set-the-notifications-config)
2. [WithCustomNotifications](#withcustomnotifications---will-set-a-custom-notifications-interface)
3. [WithMapiFeeQuotes](#withmapifeequotes---will-set-usage-of-mapi-fee-quotes-instead-of-default-fees)
4. [WithOverridenMAPIConfig](#withoverridenmapiconfig---will-override-mapi-config-with-custom-datacustom-token-endpoints-and-etc)
5. [WithMinercraft](#withminercraft---will-set-custom-minercraft-client)

</details>

1. ### WithNotifications - will set the notifications config
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithNotifications(webhookEndpoint string) ClientOps {
        return func(c *clientOptions) {
            if len(webhookEndpoint) > 0 {
                c.notifications.webhookEndpoint = webhookEndpoint
            }
        }
    }
    ```
    </details>

2. ### WithCustomNotifications - will set a custom notifications interface
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithCustomNotifications(customNotifications notifications.ClientInterface) ClientOps {
        return func(c *clientOptions) {
            if customNotifications != nil {
                c.notifications.ClientInterface = customNotifications
            }
        }
    }
    ```
    </details>

3. ### WithMapiFeeQuotes - will set usage of mapi fee quotes instead of default fees
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithMapiFeeQuotes() ClientOps {
        return func(c *clientOptions) {
            c.chainstate.options = append(c.chainstate.options, chainstate.WithMapiFeeQuotes())
        }
    }
    ```
    </details>

4. ### WithOverridenMAPIConfig - will override mApi config with custom data(custom token, endpoints and etc.)
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithOverridenMAPIConfig(miners []*minercraft.Miner) ClientOps {
        return func(c *clientOptions) {
            c.chainstate.options = append(c.chainstate.options, chainstate.WithOverridenMAPIConfig(miners))
        }
    }
    ```
    </details>

5. ### WithMinercraft - will set custom minercraft client
    <details>
    <summary>Show code</summary>
    
    ```go
    func WithMinercraft(minercraft minercraft.ClientInterface) ClientOps {
        return func(c *clientOptions) {
            if minercraft != nil {
                c.chainstate.options = append(c.chainstate.options, chainstate.WithMinercraft(minercraft))
            }
        }
    }
    ```
    </details>

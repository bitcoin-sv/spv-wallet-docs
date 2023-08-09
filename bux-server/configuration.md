# Bux Server Configuration

Bux server configuration is based on json files which are located in `config` directory and processed at the start of the server.\
You can find there files like:
- `development.json` - used for development environment
- `production.json` - used for production environment
- `test.json` - used for testing environment
- `staging.json` - used for staging environment
- `docker-compose.json` - used for docker-compose environment

#### How to use it?
You can use it by setting `BUX_ENVIRONMENT` environment variable to one of the above values.\
For example:
```bash
 BUX_ENVIRONMENT=development  
```

You can set it in your IDE or in your terminal.\
If you want to set it during starting the server, use this command:
```bash
BUX_ENVIRONMENT=development go run ./cmd/server/main.go 
```

#### Configuration file structure
```bash
{
  // COMMON SECTION
  "debug": true, // enable debug mode
  "debug_profiling": false, // enable endpoints that provides profiling information
  "disable_itc": false, // enable (ITC) incoming transaction checking
  "environment": "development", // environment name
  "import_block_headers": "", // url to import block headers
  "gdpr_compliance": false, // not used
  "request_logging": true, // enable request logging - logs every request to the server
  "use_mapi_fee_quotes": true, //  enable usage of mapi fee quotes instead of default fees
  
  // AUTHENTICATION SECTION
  "authentication": {
    "admin_key": "xpub", // admin key user for admin api
    "require_signing": false, // require endpoints signing
    "scheme": "xpub", // authentication scheme - xpub => using xPubs as tokens
    "signing_disabled": true // turns off signing !!! Only for development !!!
  },
  
  // CACHE SECTION
  "cache": {
    "engine": "freecache" // cache engine - empty/redis/freecache => (in-memory)
  },
  
  // CLUSTER SECTION
  // If settings name are strating with `_` it means that they are an example and are not used.
  // To enable this section you need to remove `_` from the beginning of the setting name.
  "_cluster": {
    "prefix": "bux_cluster_", // cluster key prefix
    "coordinator": "redis", // cluster coordinator - redis/memory
    "redis": { // redis configuration
      "url": "localhost:6379",
      "use_tls": false
    }
  },
  
  // DATASTORE SECTION
  "datastore": {
    "auto_migrate": true, // auto migrate database
    "engine": "postgresql", // database engine - postgresql/mongodb/sqlite/mysql
    "debug": false, // enable database debug mode
    "table_prefix": "xapi" // prefix for database tables, example: xapi_xpubs
  },
  
  // GRAPHQL SECTION
  "graphql": {
    "debug": true, // enable graphql debug mode
    "enabled": true, // enable graphql
    "server_path": "/graphql", // graphql server path
    "playground_path": "/graphiql" // graphql playground path
  },
  
  // MONGODB SECTION
  "mongodb": {
    "database_name": "xapi", // database name
    "transactions": false, // enable transactions
    "uri": "mongodb://localhost:27017/xapi" // mongodb connection uri
  },
  
  // MONITOR SECTION
  "monitor": {
    "auth_token": "very_secret_token", // monitor auth token
    "bux_agent_url": "test_url", // BUX agent server url address
    "debug": true, // enable monitor debug mode
    "enabled": false, // enable monitor
    "false_positive_rate": 0.01, // how many false positives do we except (default: 0.01)
    "load_monitored_destinations": true, // Whether to load monitored destinations from the database
    "max_number_of_destinations": 100000, // how many destinations can the filter hold (default: 100,000)
    "monitor_days": 14, // how many days in the past should we monitor an address (default: 7)
    "process_mempool_on_connect": false, // whether to process all transactions in the mempool when connecting to centrifuge server
    "processor_type": "bloom", // type of processor to start monitor with. Default: bloom
    "save_destinations": true // whether to save destinations on monitored transactions
  },
  
  // NEW RELIC SECTION
  "new_relic": {
    "domain_name": "domain.com", // used for hostname display
    "enabled": false, // enable new relic
    "license_key": "license-key" // new relic license key
  },
  
  // PAYMAIL SECTION
  "paymail": {
    "enabled": true, // enable paymail
    "default_from_paymail": "bux@bux.com", // default paymail address
    "default_note": "bux Address Resolution", // message needed for address resolution
    "domain_validation_enabled": false, // enable paymail domain validation. Turn off if hosted domain is not paymail related
    "domains": [ // list of allowed domains
      "localhost",
      "bux.com"
    ],
    "sender_validation_enabled": false // enable paymail sender validation. Turn on extra security
  },
  
  // REDIS SECTION
  "redis": {
    "dependency_mode": true, // enable dependency mode. Only in Redis with script enabled
    "max_active_connections": 0, // maximum number of active connections allocated by the pool at a given time
    "max_open_connections": 200, // maximum number of open connections to the database
    "max_connection_lifetime": "60s", // maximum amount of time a connection may be reused
    "max_idle_connections": 10, // maximum number of connections in the idle connection pool
    "max_idle_timeout": "10s", // maximum amount of time after which an idle connection will be closed
    "url": "redis://localhost:6379", // Redis URL connection string
    "use_tls": false // enable TLS
  },
  
  // SERVER SECTION
  "server": {
    "idle_timeout": "60s", //  maximum amount of time to wait for the next request when keep-alives are enabled. If IdleTimeout is zero, the value of ReadTimeout is used. If both are zero, there is no timeout
    "port": "3003", // port of the http server
    "read_timeout": "40s", // maximum duration for reading the entire request, including the body. A zero or negative value means there will be no timeout
    "write_timeout": "40s" // maximum duration before timing out writes of the response. A zero or negative value means there will be no timeout
  },
  
    // SQL SECTION
  "sql": {
    "host": "localhost", // host of the database
    "name": "postgres",  // name of the database
    "password": "postgres", // database password
    "port": "5432", // database port
    "replica": false, // true if it is a replica (Read-Only)
    "skip_initialize_with_version": true, // skip using MySQL in test mode
    "time_zone": "UTC", // database time zone
    "tx_timeout": "10s", // transactions timeout
    "user": "postgres" // database user
  },
  
  // SQLITE SECTION
  "sqlite": {
    "database_path": "./test-json.db", // path to the database file
    "shared": true // adds a shared param to the connection string
  },
  
  // TASK MANAGER SECTION
  "task_manager": {
    "engine": "taskq", // task manager engine - taskq, machinery
    "factory": "memory", // task manager factory - memory, redis
    "queue_name": "development_queue" // task manager queue name
  }
}
```

---
editable: false
sourcePath: en/_cli-ref/cli-ref/managed-redis/cli-ref/cluster/restore.md
---

# yc managed-redis cluster restore

Restore Redis cluster

#### Command Usage

Syntax: 

`yc managed-redis cluster restore [Flags...] [Global Flags...]`

#### Flags

| Flag | Description |
|----|----|
|`--backup-id`|<b>`string`</b><br/>ID of the backup to create a cluster from.|
|`--name`|<b>`string`</b><br/>Cluster name.|
|`--description`|<b>`string`</b><br/>Cluster description.|
|`--environment`|<b>`string`</b><br/>Cluster environment. Values: production, prestable.|
|`--network-id`|<b>`string`</b><br/>Network id.|
|`--network-name`|<b>`string`</b><br/>Network name.|
|`--host`|<b>`PROPERTY=VALUE[,PROPERTY=VALUE...]`</b><br/>Individual configurations for hosts that should be created for the Redis cluster.<br/><br/>Possible property names:<br/><ul> <li><code>zone-id</code>:     ID of the availability zone where the host resides.</li> <li><code>assign-public-ip</code>:     Whether the host should get a public IP address.</li> <li><code>replica-priority</code>:     Replica priority (100 by default)</li> <li><code>subnet-id</code>:     ID of the subnet that the host should be created in.</li> <li><code>subnet-name</code>:     Name of the subnet that the host should be created in.</li> </ul>|
|`--labels`|<b>`key=value[,key=value...]`</b><br/>A list of label KEY=VALUE pairs to add. For example, to add two labels named 'foo' and 'bar', both with the value 'baz', use '--labels foo=baz,bar=baz'.|
|`--folder-id`|<b>`string`</b><br/>ID of folder where restored cluster will be created.|
|`--enable-tls`|Enables tls for Redis cluster.|
|`--sharded`|Restore to sharded cluster (requires providing sharded backup).|
|`--persistence-mode`|<b>`string`</b><br/>Persistence mode for Redis cluster (ON, OFF or ON_REPLICAS).|
|`--announce-hostnames`|Enable announce hostnames for Redis cluster.|
|`--auth-sentinel`|Allows to use ACL users to auth in sentinel|
|`--security-group-ids`|<b>`value[,value]`</b><br/>A list of security groups for the Redis cluster.|
|`--disk-encryption-key-id`|<b>`string`</b><br/>ID of the KMS key for cluster disk encryption. Empty input will remove the use of encryption.|
|`--redis-version`|<b>`string`</b><br/>Version of Redis used in the cluster. Values: 7.2|
|`--resource-preset`|<b>`string`</b><br/>ID of the preset for computational resources available to a host (CPU, memory etc.).|
|`--disk-size`|<b>`byteSize`</b><br/>Volume of the storage available to a host in GB.|
|`--disk-type-id`|<b>`string`</b><br/>Disk type id (e.g., network-ssd).|
|`--backup-window-start`|<b>`timeofday`</b><br/>Start time for the daily backup in UTC timezone. Format: HH:MM:SS|
|`--backup-retain-period-days`|<b>`int`</b><br/>Retain period of automatically created backup in days.|
|`--datalens-access`|Allow access for DataLens|
|`--websql-access`|Allow access for Web SQL|
|`--password`|<b>`string`</b><br/>Authentication password.|
|`--max-memory-policy`|<b>`string`</b><br/>Redis maxmemory setting. Values: 'volatile-lru', 'allkeys-lru', 'volatile-lfu', 'allkeys-lfu', 'volatile-random', 'allkeys-random', 'volatile-ttl', 'noeviction'|
|`--timeout`|<b>`int`</b><br/>Time seconds that Redis keeps the connection open while the client is idle.|
|`--notify-keyspace-events`|<b>`string`</b><br/>Redis events to notify about.|
|`--slowlog-max-len`|<b>`int`</b><br/>Maximum length of slow operations log.|
|`--slowlog-log-slower-than`|<b>`int`</b><br/>Threshold in milliseconds to log slow operations.|
|`--databases`|<b>`int`</b><br/>Number of Redis databases.|
|`--maxmemory-percent`|<b>`int`</b><br/>Redis maxmemory usage in percent. Values: 1-75|
|`--client-output-buffer-limit-normal`|<b>`PROPERTY=VALUE[,PROPERTY=VALUE...]`</b><br/>Redis client output buffer limit normal limits.<br/><br/>Possible property names:<br/><ul> <li><code>hard</code>:     Redis client output buffer limit normal hard limit.</li> <li><code>soft</code>:     Redis client output buffer limit normal soft limit.</li> <li><code>secs</code>:     Redis client output buffer limit normal soft seconds.</li> </ul>|
|`--client-output-buffer-limit-pubsub`|<b>`PROPERTY=VALUE[,PROPERTY=VALUE...]`</b><br/>Redis client output buffer limit pubsub limits.<br/><br/>Possible property names:<br/><ul> <li><code>hard</code>:     Redis client output buffer limit pubsub hard limit.</li> <li><code>soft</code>:     Redis client output buffer limit pubsub soft limit.</li> <li><code>secs</code>:     Redis client output buffer limit pubsub soft seconds.</li> </ul>|
|`--lua-time-limit`|<b>`int`</b><br/>Maximum time in milliseconds for Lua scripts. Values: 0-5000, 0 - disabled mechanism|
|`--repl-backlog-size-percent`|<b>`int`</b><br/>Replication backlog size as a percentage of flavor maxmemory. Values: 1-75|
|`--cluster-require-full-coverage`|Controls whether all hash slots must be covered by nodes|
|`--cluster-allow-reads-when-down`|Allows read operations when cluster is down|
|`--cluster-allow-pubsubshard-when-down`|Allows Pub/Sub shard operations when cluster is down|
|`--lfu-decay-time`|<b>`int`</b><br/>The time, in minutes, that must elapse in order for the key counter to be divided by two (or decremented if it has a value less <= 10).|
|`--lfu-log-factor`|<b>`int`</b><br/>Determines how the frequency counter represents key hits.|
|`--turn-before-switchover`|Allows to turn before switchover in RDSync.|
|`--allow-data-loss`|Allows some data to be lost in favor of faster switchover/restart.|
|`--use-luajit`|Use JIT for lua scripts and functions.|
|`--io-threads-allowed`|Allow redis to use io-threads.|
|`--zset-max-listpack-entries`|<b>`int`</b><br/>Controls max number of entries in zset before conversion from memory-efficient listpack to CPU-efficient hash table and skiplist.|
|`--aof-max-size-percent`|<b>`int`</b><br/>AOF maximum size as a percentage of disk available. Values: 1-99|
|`--activedefrag`|Enable active (online) memory defragmentation.|
|`--async`|Display information about the operation in progress, without waiting for the operation to complete.|

#### Global Flags

| Flag | Description |
|----|----|
|`--profile`|<b>`string`</b><br/>Set the custom configuration file.|
|`--debug`|Debug logging.|
|`--debug-grpc`|Debug gRPC logging. Very verbose, used for debugging connection problems.|
|`--no-user-output`|Disable printing user intended output to stderr.|
|`--retry`|<b>`int`</b><br/>Enable gRPC retries. By default, retries are enabled with maximum 5 attempts.<br/>Pass 0 to disable retries. Pass any negative value for infinite retries.<br/>Even infinite retries are capped with 2 minutes timeout.|
|`--cloud-id`|<b>`string`</b><br/>Set the ID of the cloud to use.|
|`--folder-name`|<b>`string`</b><br/>Set the name of the folder to use (will be resolved to id).|
|`--endpoint`|<b>`string`</b><br/>Set the Cloud API endpoint (host:port).|
|`--token`|<b>`string`</b><br/>Set the OAuth token to use.|
|`--impersonate-service-account-id`|<b>`string`</b><br/>Set the ID of the service account to impersonate.|
|`--no-browser`|Disable opening browser for authentication.|
|`--format`|<b>`string`</b><br/>Set the output format: text (default), yaml, json, json-rest.|
|`--jq`|<b>`string`</b><br/>Query to select values from the response using jq syntax|
|`-h`,`--help`|Display help for the command.|

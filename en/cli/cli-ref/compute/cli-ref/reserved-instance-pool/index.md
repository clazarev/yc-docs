---
editable: false
sourcePath: en/_cli-ref/cli-ref/compute/cli-ref/reserved-instance-pool/index.md
---

# yc compute reserved-instance-pool

Manage reserved instance pools

#### Command Usage

Syntax: 

`yc compute reserved-instance-pool <command>`

Aliases: 

- `reserved-instance-pools`
- `pool`
- `pools`

#### Command Tree

- [yc compute reserved-instance-pool create](create.md) — Create a reserved instance pool
- [yc compute reserved-instance-pool delete](delete.md) — Delete the specified reserved instance pool
- [yc compute reserved-instance-pool get](get.md) — Show information about the specified reserved instance pool
- [yc compute reserved-instance-pool list](list.md) — List reserved instance pools
- [yc compute reserved-instance-pool list-instances](list-instances.md) — List instances of the specified reserved instance pool
- [yc compute reserved-instance-pool list-operations](list-operations.md) — List operations of the specified reserved instance pool
- [yc compute reserved-instance-pool update](update.md) — Update the specified reserved instance pool

#### Global Flags

| Flag | Description |
|----|----|
|`--profile`|<b>`string`</b><br/>Set the custom configuration file.|
|`--debug`|Debug logging.|
|`--debug-grpc`|Debug gRPC logging. Very verbose, used for debugging connection problems.|
|`--no-user-output`|Disable printing user intended output to stderr.|
|`--retry`|<b>`int`</b><br/>Enable gRPC retries. By default, retries are enabled with maximum 5 attempts.<br/>Pass 0 to disable retries. Pass any negative value for infinite retries.<br/>Even infinite retries are capped with 2 minutes timeout.|
|`--cloud-id`|<b>`string`</b><br/>Set the ID of the cloud to use.|
|`--folder-id`|<b>`string`</b><br/>Set the ID of the folder to use.|
|`--folder-name`|<b>`string`</b><br/>Set the name of the folder to use (will be resolved to id).|
|`--endpoint`|<b>`string`</b><br/>Set the Cloud API endpoint (host:port).|
|`--token`|<b>`string`</b><br/>Set the OAuth token to use.|
|`--impersonate-service-account-id`|<b>`string`</b><br/>Set the ID of the service account to impersonate.|
|`--no-browser`|Disable opening browser for authentication.|
|`--format`|<b>`string`</b><br/>Set the output format: text (default), yaml, json, json-rest.|
|`--jq`|<b>`string`</b><br/>Query to select values from the response using jq syntax|
|`-h`,`--help`|Display help for the command.|

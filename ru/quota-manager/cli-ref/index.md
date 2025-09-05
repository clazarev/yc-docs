---
editable: false
sourcePath: en/_cli-ref/cli-ref/quota-manager/cli-ref/index.md
---

# yc quota-manager

Manage Yandex Quota Manager resources

#### Command Usage

Syntax: 

`yc quota-manager <group>`

#### Command Tree

- [yc quota-manager quota-limit](quota-limit/index.md) — Manage quota limits
	- [yc quota-manager quota-limit get](quota-limit/get.md) — Show information about the specified quota
	- [yc quota-manager quota-limit list](quota-limit/list.md) — List quota limits
	- [yc quota-manager quota-limit list-services](quota-limit/list-services.md) — List quota services
- [yc quota-manager quota-request](quota-request/index.md) — Manage quota requests
	- [yc quota-manager quota-request cancel](quota-request/cancel.md) — Cancel quotas in the specified quota request.
	- [yc quota-manager quota-request create](quota-request/create.md) — Create a quota request
	- [yc quota-manager quota-request get](quota-request/get.md) — Show information about the specified quota request
	- [yc quota-manager quota-request list](quota-request/list.md) — List quota requests
	- [yc quota-manager quota-request list-operations](quota-request/list-operations.md) — List operations for the specified quota request

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

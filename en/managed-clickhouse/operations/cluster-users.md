---
title: Managing {{ CH }} cluster users in {{ mch-full-name }}
description: In this guide, you will learn how to manage {{ mch-name }} cluster users.
---

# User management {{ CH }}

{{ mch-name }} provides two methods for managing users and their individual settings:

* Using the native {{ yandex-cloud }} interfaces, such as the management console, CLI, {{ TF }}, or API . Select this method to create, update, and delete users and custom user settings using {{ mch-full-name }} features.
* Using SQL queries to the cluster. Select this method to use solutions you already have to create and manage users or if you are using [RBAC](https://en.wikipedia.org/wiki/Role-based_access_control).

{% note warning %}

In a {{ mch-name }} cluster, you can only use one user management method at a time: either via native interfaces or via SQL queries.

{% endnote %}

{% note info %}

Creating a new {{ CH }} cluster automatically creates [service users](../concepts/service-users.md) to administer and monitor the service.

{% endnote %}

## User management via SQL {#sql-user-management}


To enable this management method, select **{{ ui-key.yacloud.mdb.forms.database_field_sql-user-management }}** when [creating](cluster-create.md) or [reconfiguring](update.md#SQL-management) your cluster.

{% note warning %}

You cannot disable the SQL user management setting once it is enabled.

{% endnote %}

In a cluster with user management via SQL enabled:

* User management via the native {{ yandex-cloud }} interfaces, such as the management console, CLI, API, and {{ TF }}, is unavailable.
* The existing users as well as user settings created with the native {{ yandex-cloud }} interfaces will remain unchanged.
* Users are managed by the `admin` account. You set the `admin` password when selecting the **{{ ui-key.yacloud.mdb.forms.database_field_sql-user-management }}** option.

For more information about managing users via SQL, see [this {{ CH }} article]({{ ch.docs }}/operations/access-rights).

## Getting a list of users {#list-users}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), navigate to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Click the name of your cluster and open the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To get a list of cluster users, run the following command:

  ```bash
  {{ yc-mdb-ch }} user list
     --cluster-name=<cluster_name>
  ```

  You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters).

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Use the [User.List](../api-ref/User/list.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

        ```bash
        curl \
            --request GET \
            --header "Authorization: Bearer $IAM_TOKEN" \
            --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<cluster_ID>/users'
        ```

        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/User/list.md#responses) to make sure your request was successful.

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Use the [UserService.List](../api-ref/grpc/User/list.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/user_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                    "cluster_id": "<cluster_ID>"
                }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.clickhouse.v1.UserService.List
        ```

        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/grpc/User/list.md#yandex.cloud.mdb.clickhouse.v1.ListUsersResponse) to make sure your request was successful.

- SQL {#sql}

  1. [Connect](connect/clients.md) to the cluster as [`admin`](#sql-user-management).
  1. Get a list of users:

      ```sql
      SHOW USERS;
      ```

{% endlist %}

## Creating a user {#adduser}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), navigate to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Click the name of your cluster and open the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab.
  1. Click **{{ ui-key.yacloud.mdb.cluster.users.action_add-user }}**.
  1. Enter a DB username.

      {% include [user-name](../../_includes/mdb/mch/note-info-user-name-and-pass-limits.md) %}

  
  1. Select how to set a password:

      * **{{ ui-key.yacloud.component.password-input.label_button-enter-manually }}**: Enter your own password. The password must be from 8 to 128 characters long.

      * **{{ ui-key.yacloud.component.password-input.label_button-generate }}**: Generate a password with {{ connection-manager-name }}.

      To view the password, select the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab on the cluster page and click **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** in the new user row. This will open the page of the {{ lockbox-name }} secret that stores your password. To view passwords, you need the `lockbox.payloadViewer` role.


  1. Select one or more databases the user should have access to:
     1. Click ![image](../../_assets/console-icons/plus.svg) and select a database from the drop-down list.
     1. Repeat the previous step until all the required databases are selected.
     1. To delete a database added by mistake, click ![image](../../_assets/console-icons/xmark.svg) to the right of the database name.
  1. Configure [additional settings](../concepts/settings-list.md) for the user:
     1. Set [quotas](../concepts/settings-list.md#quota-settings) under **{{ ui-key.yacloud.mdb.cluster.users.button_advanced-settings }} → Quotas**:
        1. To add a quota, click ![image](../../_assets/console-icons/plus.svg). You can add multiple quotas that will be valid at the same time.
        1. To delete a quota, click ![image](../../_assets/console-icons/ellipsis.svg) to the right of the quota name and select **{{ ui-key.yacloud.component.mdb.settings.label_delete }}**.
        1. To change a quota, specify the required values in its settings.
     1. Configure [{{ CH }}](../concepts/settings-list.md#user-level-settings) under **{{ ui-key.yacloud.mdb.cluster.users.button_advanced-settings }} → Settings**.
  1. Click **{{ ui-key.yacloud.mdb.cluster.users.popup-button_add }}**.

  See also the [example of creating a user with read-only access permissions](#example-create-readonly-user).

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To create a user in a cluster, run this command:

  ```bash
  {{ yc-mdb-ch }} user create <username> \
     --cluster-name=<cluster_name> \
     --password=<user_password> \
     --permissions=<DB_list> \
     --quota=<list_of_single_quota_settings_for_user> \
     --settings=<list_of_{{ CH }}_settings_for_user>
  ```

  Where:

  * `--cluster-name`: Cluster name.
  * `--password`: User password. The password must be from 8 to 128 characters long.

      
      You can also generate a password with {{ connection-manager-name }}. To do this, specify `--generate-password` instead of `--password=<password>`.

      To view the password, select the cluster you need in the [management console]({{ link-console-main }}), go to the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab, and click **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** in the new user row. This will open the page of the {{ lockbox-name }} secret that stores your password. To view passwords, you need the `lockbox.payloadViewer` role.


  * `--permissions`: List of DBs the user should have access to.

  For more information about [quotas](../concepts/settings-list.md#quota-settings) and [query-level settings](../concepts/settings-list.md#user-level-settings), see [{#T}](../concepts/settings-list.md).

  To set multiple quotas, list them by specifying the `--quota` parameter once for each quota in the following command:

  ```bash
  {{ yc-mdb-ch }} user create <username> \
     ...
     --quota="<quota_0_settings>" \
     --quota="<quota_1_settings>" \
     ...
  ```

  You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters).

  See also the [example of creating a user with read-only access permissions](#example-create-readonly-user).

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file that defines your infrastructure.

        For more information about creating this file, see [this guide](cluster-create.md).

    1. Add the `yandex_mdb_clickhouse_user` resource:

        ```hcl
        resource "yandex_mdb_clickhouse_user" "<username>" {
          cluster_id = "<cluster_ID>"
          name       = "<username>"
          password   = "<password>"
          permission {
            database_name = "<DB_name>"
          }
          settings {
            <parameter_1_name> = <value_1>
            <parameter_2_name> = <value_2>
            ...
          }
        }
        ```

        {% include [user-name](../../_includes/mdb/mch/note-info-user-name-and-pass-limits.md) %}

        The password must be from 8 to 128 characters long.

        
        You can also generate a password using {{ connection-manager-name }}. To do this, specify `generate_password = true` instead of `password = "<password>"`.

        To view the password, select the cluster you need in the [management console]({{ link-console-main }}), go to the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab, and click **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** in the new user row. This will open the page of the {{ lockbox-name }} secret that stores your password. To view passwords, you need the `lockbox.payloadViewer` role.


        When creating a cluster with {{ TF }} at the same time as creating a user, specify a name for the new cluster rather than cluster ID in the `yandex_mdb_clickhouse_user` resource:

        ```hcl

        resource "yandex_mdb_clickhouse_cluster" "<cluster_name>" {
          name = "<cluster_name>"
          ...
        }

        resource "yandex_mdb_clickhouse_user" "<username>" {
          cluster_id = yandex_mdb_clickhouse_cluster.<cluster_name>.id
          name       = "<username>"
          ...
        }
        ```

    1. Make sure the settings are correct.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    For more information, see [this {{ TF }} provider article]({{ tf-provider-resources-link }}/mdb_clickhouse_user).

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Use the [User.Create](../api-ref/User/create.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

        1. Create a file named `body.json` and paste the following code into it:

            ```json
            {
              "userSpec": {
                "name": "<username>",
                "password": "<user_password>",
                "permissions": [
                  {
                    "databaseName": "<DB_name>"
                  }
                ],
                "settings": {<{{ CH }}_settings>},
                "quotas": [
                  {
                    "intervalDuration": "<quota_interval>",
                    "queries": "<total_number_of_queries>",
                    "errors": "<number_of_failed_queries>",
                    "resultRows": "<number_of_result_rows>",
                    "readRows": "<number_of_source_rows>",
                    "executionTime": "<total_execution_time>"
                  },
                  { <similar_settings_for_quota_2> },
                  { ... },
                  { <similar_settings_for_quota_N> }
                ]
              },
              { <similar_settings_for_new_user_2> },
              { ... },
              { <similar_settings_for_new_user_N> }
            }
            ```

            Where `userSpec` is the array of the new users' settings. One array element contains settings for a single user and has the following structure:

            {% include [rest-user-specs](../../_includes/mdb/mch/api/rest-user-specs.md) %}

            * `settings`: List of [{{ CH }} settings](../concepts/settings-list.md#user-level-settings) for the user.

                Settings are specified as comma-separated `key: value` pairs.

            * `quotas`: Array of [quota settings](../concepts/settings-list.md#quota-settings). One array element contains settings for a single quota.

        1. Run this request:

            ```bash
            curl \
              --request POST \
              --header "Authorization: Bearer $IAM_TOKEN" \
              --header "Content-Type: application/json" \
              --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<cluster_ID>/users' \
              --data '@body.json'
            ```

            You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/User/create.md#responses) to make sure your request was successful.

    See also the [example of creating a user with read-only access permissions](#example-create-readonly-user).

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Use the [ClusterService.Create](../api-ref/grpc/User/create.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

        1. Create a file named `body.json` and paste the following code into it:

            ```json
            {
              "cluster_id": "<cluster_ID>",
              "user_spec": {
                "name": "<username>",
                "password": "<user_password>",
                "permissions": [
                  {
                    "database_name": "<DB_name>"
                  }
                ],
                "settings": {<{{ CH }}_settings>},
                "quotas": [
                  {
                    "interval_duration": "<quota_interval>",
                    "queries": "<total_number_of_queries>",
                    "errors": "<number_of_failed_queries>",
                    "result_rows": "<number_of_result_rows>",
                    "read_rows": "<number_of_source_rows>",
                    "execution_time": "<total_execution_time>"
                  },
                  { <similar_settings_for_quota_2> },
                  { ... },
                  { <similar_settings_for_quota_N> }
                ]
              },
              { <similar_settings_for_new_user_2> },
              { ... },
              { <similar_settings_for_new_user_N> }
            }
            ```

            Where `user_spec` is the array of the new users' settings. One array element contains settings for a single user and has the following structure:

            {% include [grpc-user-specs](../../_includes/mdb/mch/api/grpc-user-specs.md) %}

            * `settings`: List of [{{ CH }} settings](../concepts/settings-list.md#user-level-settings) for the user.

                Settings are specified as comma-separated `key: value` pairs.

            * `quotas`: Array of [quota settings](../concepts/settings-list.md#quota-settings). One array element contains settings for a single quota.

            You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

        1. Run this query:

            ```bash
            grpcurl \
              -format json \
              -import-path ~/cloudapi/ \
              -import-path ~/cloudapi/third_party/googleapis/ \
              -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/user_service.proto \
              -rpc-header "Authorization: Bearer $IAM_TOKEN" \
              -d @ \
              {{ api-host-mdb }}:{{ port-https }} \
              yandex.cloud.mdb.clickhouse.v1.UserService.Create \
              < body.json
            ```

    1. View the [server response](../api-ref/grpc/User/create.md#yandex.cloud.operation.Operation) to make sure your request was successful.

    See also the [example of creating a user with read-only access permissions](#example-create-readonly-user).

- SQL {#sql}

  1. [Connect](connect/clients.md) to your cluster as [`admin`](#sql-user-management).
  1. Create a user:

      ```sql
      CREATE USER <username> IDENTIFIED WITH sha256_password BY '<user_password>';
      ```

      {% include [sql-user-name-and-password-limits](../../_includes/mdb/mch/note-sql-info-user-name-and-pass-limits.md) %}

  For more information about creating users, see [this {{ CH }} article]({{ ch.docs }}/sql-reference/statements/create/user/).

{% endlist %}

## Changing a password {#updateuser}

We recommend using the {{ yandex-cloud }} interfaces listed below. Do not use SQL to change your password; otherwise, the password may reset to the previous one after [maintenance](../concepts/maintenance.md).

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), navigate to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Click the name of your cluster and open the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab.
  1. Click ![image](../../_assets/console-icons/ellipsis.svg) and select **{{ ui-key.yacloud.mdb.cluster.users.button_action-password }}**.

  
  1. Select how to set a new password:

      * **{{ ui-key.yacloud.component.password-input.label_button-enter-manually }}**: Enter your own password. The password must be from 8 to 128 characters long.

      * **{{ ui-key.yacloud.component.password-input.label_button-generate }}**: Generate a password with {{ connection-manager-name }}.


  1. Click **{{ ui-key.yacloud.mdb.cluster.users.popup-password_button_change }}**.

  
  To view the new password, select the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab on the cluster page and click **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** in the user's row. This will open the page of the {{ lockbox-name }} secret that stores your password. The new password version is labeled as **{{ ui-key.yacloud.lockbox.label_version-current }}**.

  To view passwords, you need the `lockbox.payloadViewer` role.


- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To change a user password, run this command:

  ```bash
  {{ yc-mdb-ch }} user update <username> \
     --cluster-name=<cluster_name> \
     --password=<new_password>
  ```

  The password must be from 8 to 128 characters long.

  
  You can also generate a new password with {{ connection-manager-name }}. To do this, specify `--generate-password` instead of `--password=<new_password>`.

  To view the new password, select the cluster in the [management console]({{ link-console-main }}), go to the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab, and click **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** in the the user's row. This will open the page of the {{ lockbox-name }} secret that stores your password. The new password version is labeled as **{{ ui-key.yacloud.lockbox.label_version-current }}**.

  To view passwords, you need the `lockbox.payloadViewer` role.


  You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters).

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file that defines your infrastructure.

        For more information about creating this file, see [this guide](cluster-create.md).

    1. Locate the user's `yandex_mdb_clickhouse_user` resource.

    1. Change the value of the `password` field:

        ```hcl
        resource "yandex_mdb_clickhouse_user" "<username>" {
          ...
          name     = "<username>"
          password = "<password>"
          ...
        }
        ```

        The password must be from 8 to 128 characters long.

        
        You can also generate a new password with {{ connection-manager-name }}. To do this, specify `generate_password = true` instead of `password = "<new_password>"`.

        To view the new password, select the cluster in the [management console]({{ link-console-main }}), go to the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab, and click **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** in the the user's row. This will open the page of the {{ lockbox-name }} secret that stores your password. The new password version is labeled as **{{ ui-key.yacloud.lockbox.label_version-current }}**.

        {% note info %}

        If the old password was generated, you cannot regenerate it using {{ TF }} due to provider limitations.

        {% endnote %}


    1. Validate your configuration.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    For more information, see [this {{ TF }} provider article]({{ tf-provider-resources-link }}/mdb_clickhouse_user).

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Use the [User.Update](../api-ref/User/update.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

        {% include [note-updatemask](../../_includes/note-api-updatemask.md) %}

        ```bash
        curl \
            --request PATCH \
            --header "Authorization: Bearer $IAM_TOKEN" \
            --header "Content-Type: application/json" \
            --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<cluster_ID>/users/<username>' \
            --data '{
                      "updateMask": "password",
                      "password": "<new_password>"
                    }'
        ```

        Where:

        * `updateMask`: List of parameters to update as a single string, separated by commas.

            Here, we only specified a single parameter, `password`.

        * `password`: New password. The password must be from 8 to 128 characters long.

           
           You can also generate a password with {{ connection-manager-name }}. To do this, edit the contents of the `data` field:

           ```bash
           {
             "updateMask": "generatePassword",
             "generatePassword": true
           }
           ```

           To view the new password, select the cluster in the [management console]({{ link-console-main }}), go to the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab, and click **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** in the the user's row. This will open the page of the {{ lockbox-name }} secret that stores your password. The new password version is labeled as **{{ ui-key.yacloud.lockbox.label_version-current }}**.

           To view passwords, you need the `lockbox.payloadViewer` role.


        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters). You can get the username with the [list of users in the cluster](#list-users).

    1. View the [server response](../api-ref/User/update.md#responses) to make sure your request was successful.

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Use the [UserService.Update](../api-ref/grpc/User/update.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

        {% include [note-grpc-updatemask](../../_includes/note-grpc-api-updatemask.md) %}

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/user_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                  "cluster_id": "<cluster_ID>",
                  "user_name": "<username>",
                  "update_mask": {
                    "paths": [
                      "password"
                    ]
                  },
                  "password": "<new_password>"
                }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.clickhouse.v1.UserService.Update
        ```

        Where:

        * `update_mask`: List of parameters to update as an array of `paths[]` strings.

            Here, we only specified a single parameter, `password`.

        * `password`: New password. The password must be from 8 to 128 characters long.

           
           You can also generate a password with {{ connection-manager-name }}. To do this, edit the contents of the `d` parameter:

           ```bash
           {
             "cluster_id": "<cluster_ID>",
             "user_name": "<username>",
             "update_mask": {
               "paths": [
                 "generate_password"
               ]
             },
             "generate_password": true
           }
           ```

           To view the new password, select the cluster in the [management console]({{ link-console-main }}), go to the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab, and click **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** in the the user's row. This will open the page of the {{ lockbox-name }} secret that stores your password. The new password version is labeled as **{{ ui-key.yacloud.lockbox.label_version-current }}**.

           To view passwords, you need the `lockbox.payloadViewer` role.


        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters). You can get the username with the [list of users in the cluster](#list-users).

    1. View the [server response](../api-ref/grpc/User/update.md#yandex.cloud.operation.Operation) to make sure your request was successful.

{% endlist %}

## Changing the admin password {#admin-password-change}

We recommend using the {{ yandex-cloud }} interfaces listed below. Do not use SQL to change your password; otherwise, the password may reset to the previous one after [maintenance](../concepts/maintenance.md).

{% list tabs group=instructions %}

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To change the `admin` password, run the following command:

    ```bash
    {{ yc-mdb-ch }} cluster update <cluster_name_or_ID> \
      --admin-password <new_admin_password>
    ```

    {% include [password-limits](../../_includes/mdb/mch/note-info-password-limits.md) %}

    You can request the cluster ID and name with the [list of clusters in the folder](cluster-list.md#list-clusters).

    {% note tip %}

    * For enhanced security, use the `--read-admin-password` parameter rater than `--admin-password`, as you will need to enter the new password with your keyboard, and it will not be saved in the command history.
    * To generate a password automatically, use `--generate-admin-password`. The command output will contain the new password.

    {% endnote %}

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file that defines your infrastructure.

        For more information about creating this file, see [this guide](cluster-create.md).

    1. Change the value of the `admin_password` field:

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<cluster_name>" {
          ...
          admin_password = "<admin_password>"
          ...
        }
        ```

        {% include [password-limits](../../_includes/mdb/mch/note-info-password-limits.md) %}

    1. Make sure the settings are correct.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    For more information, see [this {{ TF }} provider article]({{ tf-provider-mch }}).

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Use the [Cluster.Update](../api-ref/Cluster/update.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

        {% include [note-updatemask](../../_includes/note-api-updatemask.md) %}

        ```bash
        curl \
            --request PATCH \
            --header "Authorization: Bearer $IAM_TOKEN" \
            --header "Content-Type: application/json" \
            --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<cluster_ID>' \
            --data '{
                      "updateMask": "configSpec.adminPassword",
                      "configSpec": {
                        "adminPassword": "<new_password>"
                      }
                    }'
        ```

        Where:

        * `updateMask`: List of parameters to update as a single string, separated by commas.

            Here, we only specified a single parameter, `configSpec.adminPassword`.

        * `configSpec.adminPassword`: New user password.

            The password must be from 8 to 128 characters long.

        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/Cluster/update.md#responses) to make sure your request was successful.

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Use the [ClusterService.Update](../api-ref/grpc/Cluster/update.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

        {% include [note-grpc-updatemask](../../_includes/note-grpc-api-updatemask.md) %}

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/cluster_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                  "cluster_id": "<cluster_ID>",
                  "update_mask": {
                    "paths": [
                      "config_spec.admin_password"
                    ]
                  },
                  "config_spec": {
                    "admin_password": "<new_password>"
                  }
                }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.clickhouse.v1.ClusterService.Update
        ```

        Where:

        * `update_mask`: List of parameters to update as an array of `paths[]` strings.

            Here, we only specified a single parameter, `config_spec.admin_password`.

        * `config_spec.admin_password`: New user password.

            The password must be from 8 to 128 characters long.

        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters).

    1. View the [server response](../api-ref/grpc/Cluster/update.md#yandex.cloud.operation.Operation) to make sure your request was successful.

{% endlist %}

## Changing user settings {#update-settings}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), navigate to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Click the name of your cluster and open the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab.
  1. Click ![image](../../_assets/console-icons/ellipsis.svg) and select **{{ ui-key.yacloud.mdb.cluster.users.button_action-update }}**.
  1. Configure user permissions to access specific databases:
     1. To grant access to the required databases:
        1. Click ![image](../../_assets/console-icons/plus.svg) and select a database from the drop-down list.
        1. Repeat the previous step until all the required databases are selected.
     1. To delete a database, click ![image](../../_assets/console-icons/xmark.svg) to the right of the database name.
  1. Set [quotas](../concepts/settings-list.md#quota-settings) for the user under **{{ ui-key.yacloud.mdb.cluster.users.button_advanced-settings }} → Quotas**:
     1. To add a quota, click ![image](../../_assets/console-icons/plus.svg). You can add multiple quotas that will be valid at the same time.
     1. To delete a quota, click ![image](../../_assets/console-icons/ellipsis.svg) to the right of the quota name and select **{{ ui-key.yacloud.component.mdb.settings.label_delete }}**.
     1. To change a quota, specify the required values in its settings.
  1. Change the user [{{ CH }} settings](../concepts/settings-list.md#dbms-user-settings) under **{{ ui-key.yacloud.mdb.cluster.users.button_advanced-settings }} → Settings**.
  1. Click **{{ ui-key.yacloud.mdb.cluster.users.popup-button_save }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  You can change user settings from the command line interface:
  1. To set up user permissions to access specific databases, run the command by listing the database names in the `--permissions` parameter:

     ```bash
     {{ yc-mdb-ch }} user update <username> \
        --cluster-name=<cluster_name> \
        --permissions=<DB_list>
     ```

     You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters).

     This command grants the user access permissions for the listed databases.

     To revoke access to a specific database, remove its name from the list and provide the updated list to the command.

  1. To change user's [quota settings](../concepts/settings-list.md#quota-settings), run the following command, listing all quotas by specifying the `--quota` parameter for each one:

     ```bash
     {{ yc-mdb-ch }} user update <username> \
        --cluster-name=<cluster_name> \
        --quota=<quota_0_settings_(unchanged)> \
        --quota=<quota_1_settings_(unchanged)> \
        --quota=<quota_2_settings_(changed)> \
        --quota=<quota_3_settings_(unchanged)> \
        --quota=<quota_4_settings_(changed)> \
        --quota=<quota_5_settings_(new_quota)>
       ...
     ```

     You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters).

     This command overwrites all existing user quota settings with the new ones you provided to the command.
     Before running the command, make sure you included the settings for new and changed quotas as well as the settings for existing quotas that have not changed.

     To delete one or more user quotas, exclude their settings from the list and provide the updated list of `--quota` parameters to the command.

     When setting a quota interval, you can use units hours (`h`), minutes (`m`), seconds (`s`), and milliseconds (`ms`) in the value, e.g., `3h20m10s7000ms`. The resulting value is still expressed in milliseconds, e.g., `12017000`. The interval value must be a multiple of 1,000 milliseconds. For example, `1s500ms` is incorrect.

  1. To update user's [{{ CH }} settings](../concepts/settings-list.md#dbms-user-settings), run the command below, listing the changed settings using the `--settings` parameter:

     ```bash
     {{ yc-mdb-ch }} user update <username> \
        --cluster-name=<cluster_name> \
        --settings=<list_of_{{ CH }}>_settings
     ```

     You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters).

     The command only updates the settings that are explicitly specified in the `--settings` parameter. For example, the command with the `--settings="readonly=1"` parameter will only update the `readonly` setting and will not reset the values of the other settings. This is how changing {{ CH }} settings differs from changing quota settings.

     You cannot use this command to delete an existing setting. You can only explicitly set it to its default value (specified for [each setting](#clickhouse-settings)).

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file that defines your infrastructure.

        For more information about creating this file, see [this guide](cluster-create.md).

    1. Locate the user's `yandex_mdb_clickhouse_user` resource.

    1. To set up user permissions to access specific databases, add the required number of `permission` sections, one per database:

        ```hcl
        resource "yandex_mdb_clickhouse_user" "<username>" {
          ...
          name       = "<username>"
          password   = "<password>"
          permission {
            database_name = "<DB_1_name>"
          }
          ...
          permission {
            database_name = "<DB_N_name>"
          }
          ...
        }
        ```

        In the `database_name` field, specify the name of the database to grant access to.

    1. To update user's [quota settings](../concepts/settings-list.md#quota-settings), add the required number of `quota` sections to the cluster user description.

        When describing quotas, only the `interval_duration` field is required.

        ```hcl
        resource "yandex_mdb_clickhouse_user" "<username>" {
          ...
          name       = "<username>"
          password   = "<password>"
          ...
          quota {
            interval_duration = <interval_in_milliseconds>
            ...
          }
        }
        ```

    1. To update user's [{{ CH }} settings](../concepts/settings-list.md#dbms-user-settings), add the `settings` section to the cluster user description.

        ```hcl
        resource "yandex_mdb_clickhouse_user" "<username>" {
          ...
          name       = "<username>"
          password   = "<password>"
          ...
          settings {
            <parameter_1_name> = <value_1>
            <parameter_2_name> = <value_2>
            ...
          }
        }
        ```

    1. Make sure the settings are correct.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    For more information, see [this {{ TF }} provider article]({{ tf-provider-resources-link }}/mdb_clickhouse_user).

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Use the [User.Update](../api-ref/User/update.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

        {% include [note-updatemask](../../_includes/note-api-updatemask.md) %}

        ```bash
        curl \
            --request PATCH \
            --header "Authorization: Bearer $IAM_TOKEN" \
            --header "Content-Type: application/json" \
            --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<cluster_ID>/users/<username>' \
            --data '{
                      "updateMask": "<list_of_settings_to_update>",
                      "permissions": [ <updated_DB_list> ],
                      "settings": { <{{ CH }}_settings> },
                      "quotas": [ <updated_list_of_quota_settings> ]
                    }'
        ```

        Where `updateMask` is the list of parameters to update as a single string, separated by commas.

        Specify the required parameters to update individual categories of settings:

        * To update the list of databases available to the user, provide the updated list in the `permissions` parameter.

            The list appears as an array of `databaseName` parameters. Each parameter contains the name of a separate database.

            {% note warning %}

            The current DB list in the cluster will be completely overwritten by the list provided in the `permissions` parameter.

            Before sending your request, make sure the list covers all the required databases, including existing ones.

            {% endnote %}

        * To update user's [{{ CH }} settings](../concepts/settings-list.md#user-level-settings), provide the required settings with updated values in the `settings` parameter.

        * To update [quota settings](../concepts/settings-list.md#quota-settings), provide the updated list of settings in the `quotas` parameter.

            The list appears as an array. One array element contains settings for a single quota.

            {% note warning %}

            The current list of quota settings in the cluster will be completely overwritten by the list provided in the `quotas` parameter.

            Before sending your request, make sure the list covers all the required quota settings, including existing ones.

            {% endnote %}

        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters). You can get the username with the [list of users in the cluster](#list-users).

    1. View the [server response](../api-ref/User/update.md#responses) to make sure your request was successful.

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Use the [UserService.Update](../api-ref/grpc/User/update.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

        {% include [note-grpc-updatemask](../../_includes/note-grpc-api-updatemask.md) %}

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/user_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                  "cluster_id": "<cluster_ID>",
                  "user_name": "<username>",
                  "update_mask": {
                    "paths": [
                      <list_of_settings_to_update>
                    ]
                  },
                  "permissions": [ <updated_DB_list> ],
                  "settings": { <{{ CH }}_settings> },
                  "quotas": [ <updated_list_of_quota_settings> ]
                }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.clickhouse.v1.UserService.Update
        ```

        Where `update_mask` is the list of parameters to update as an array of `paths[]` strings.

        Specify the required parameters to update individual categories of settings:

        * To update the list of databases available to the user, provide the updated list in the `permissions` parameter.

            The list appears as an array of `database_name` parameters. Each parameter contains the name of a separate database.

            {% note warning %}

            The current DB list in the cluster will be completely overwritten by the list provided in the `permissions` parameter.

            Before sending your request, make sure the list covers all the required databases, including existing ones.

            {% endnote %}

        * To update user's [{{ CH }} settings](../concepts/settings-list.md#user-level-settings), provide the required settings with updated values in the `settings` parameter.

        * To update [quota settings](../concepts/settings-list.md#quota-settings), provide the updated list of settings in the `quotas` parameter.

            The list appears as an array. One array element contains settings for a single quota.

            {% note warning %}

            The current list of quota settings in the cluster will be completely overwritten by the list provided in the `quotas` parameter.

            Before sending your request, make sure the list covers all the required quota settings, including existing ones.

            {% endnote %}

        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters). You can get the username with the [list of users in the cluster](#list-users).

    1. View the [server response](../api-ref/grpc/User/update.md#yandex.cloud.operation.Operation) to make sure your request was successful.

- SQL {#sql}

  1. [Connect](connect/clients.md) to your cluster as [`admin`](#sql-user-management).
  1. To change a set of user privileges and roles, use the [GRANT]({{ ch.docs }}/sql-reference/statements/grant/) and [REVOKE]({{ ch.docs }}/sql-reference/statements/revoke/) statements. For example, grant the user read permissions for all objects in a specific database:

      ```sql
      GRANT SELECT ON <DB_name>.* TO <username>;
      ```

  1. To update user's [quota settings](../concepts/settings-list.md#quota-settings), use the [CREATE QUOTA]({{ ch.docs }}/sql-reference/statements/create/quota/#create-quota-statement), [ALTER QUOTA]({{ ch.docs }}/sql-reference/statements/alter/quota/#alter-quota-statement), and [DROP QUOTA]({{ ch.docs }}/sql-reference/statements/drop/#drop-quota-statement) statements. For example, limit the total number of user queries for a 15-month period:

      ```sql
      CREATE QUOTA <quota_name> FOR INTERVAL 15 MONTH MAX QUERIES 100 TO <username>;
      ```

  1. To change a user account, use the [ALTER USER]({{ ch.docs }}/sql-reference/statements/alter/user/) statement. For example, to update [{{ CH }} settings](../concepts/settings-list.md#dbms-user-settings), run the command below, listing the settings to update:

      ```sql
      ALTER USER <username> SETTINGS <list_of_{{ CH }}>_settings;
      ```

{% endlist %}

## Deleting a user {#removeuser}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), navigate to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Click the name of your cluster and open the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab.
  1. Click ![image](../../_assets/console-icons/ellipsis.svg) and select **{{ ui-key.yacloud.mdb.clusters.button_action-delete }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To delete a user, run this command:

  ```bash
  {{ yc-mdb-ch }} user delete <username> \
     --cluster-name=<cluster_name>
  ```

  You can get the cluster name with the [list of clusters in the folder](cluster-list.md#list-clusters).

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file that defines your infrastructure.

        For more information about creating this file, see [this guide](cluster-create.md).

    1. Delete the `yandex_mdb_clickhouse_user` resource with the user description.

    1. Validate your configuration.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    For more information, see [this {{ TF }} provider article]({{ tf-provider-resources-link }}/mdb_clickhouse_user).

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Use the [User.Delete](../api-ref/User/delete.md) method and send the following request, e.g., via {{ api-examples.rest.tool }}:

        ```bash
        curl \
            --request DELETE \
            --header "Authorization: Bearer $IAM_TOKEN" \
            --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<cluster_ID>/users/<username>'
        ```

        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters). You can get the username with the [list of users in the cluster](#list-users).

    1. View the [server response](../api-ref/User/delete.md#responses) to make sure your request was successful.

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Use the [UserService.Delete](../api-ref/grpc/User/delete.md) call and send the following request, e.g., via {{ api-examples.grpc.tool }}:

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/user_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                    "cluster_id": "<cluster_ID>",
                    "user_name": "<username>"
                }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.clickhouse.v1.UserService.Delete
        ```

        You can get the cluster ID with the [list of clusters in the folder](./cluster-list.md#list-clusters). You can get the username with the [list of users in the cluster](#list-users).

    1. View the [server response](../api-ref/grpc/User/delete.md#yandex.cloud.operation.Operation) to make sure your request was successful.

- SQL {#sql}

  1. [Connect](connect/clients.md) to your cluster as [`admin`](#sql-user-management).
  1. Delete the user:

      ```sql
      DROP USER <username>;
      ```

  To learn more about deleting objects, see [this {{ CH }} article]({{ ch.docs }}/sql-reference/statements/drop/).

{% endlist %}

## Examples {#examples}

### Creating a read-only user {#example-create-readonly-user}

Let's say you need to add a new user named `ro-user` with the `Passw0rd` password to the existing `mych` cluster with the `{{ cluster-id }}` ID, and:
* The user has access to the `db1` database of the cluster.
* The access is read-only, with no option to change any settings.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), navigate to the folder dashboard and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Click the `mych` cluster and select the **{{ ui-key.yacloud.clickhouse.cluster.switch_users }}** tab.
  1. Click **{{ ui-key.yacloud.mdb.cluster.users.action_add-user }}**.
  1. Enter `ro-user` as the DB username and `Passw0rd` as the password.
  1. Click ![image](../../_assets/console-icons/plus.svg) and select `db1` from the drop-down list.
  1. Select **{{ ui-key.yacloud.mdb.cluster.users.button_advanced-settings }} → Settings → Readonly**.
  1. Set the **Readonly** field value to `1`.
  1. Click **{{ ui-key.yacloud.mdb.cluster.users.popup-button_add }}**.

- CLI {#cli}

  Run this command:

  ```bash
  {{ yc-mdb-ch }} user create "ro-user" \
     --cluster-name="mych" \
     --password="Passw0rd" \
     --permissions="db1" \
     --settings="readonly=1"
  ```

    Once you create the user, make sure it actually has read-only access:
    1. [Connect](connect/clients.md) to the `mych` cluster as `ro-user` you created.
    1. Try changing any setting, e.g., disable read-only mode:

       ```
       SET readonly=0
       ```

       As a result, the command should return a message stating that you cannot change the setting in read-only mode:

       ```
       DB::Exception: Cannot modify 'readonly' setting in readonly mode.
       ```

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file that defines your infrastructure.

        For more information about creating this file, see [this guide](cluster-create.md).

    1. Add the `yandex_mdb_clickhouse_user` resource:

        ```hcl
        resource "yandex_mdb_clickhouse_user" "ro-user" {
          cluster_id = "cat0adul1fj0********"
          name = "ro-user"
          password = "Passw0rd"
          permission {
            database_name = "db1"
          }
          settings {
            readonly = 1
          }
        }
        ```

    1. Make sure the settings are correct.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

- REST API {#api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. Make a request using {{ api-examples.rest.tool }}:

        ```bash
        curl \
            --request POST \
            --header "Authorization: Bearer $IAM_TOKEN" \
            --header "Content-Type: application/json" \
            --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/{{ cluster-id }}/users' \
            --data '{
                      "userSpec": {
                        "name": "ro-user",
                        "password": "Passw0rd",
                        "permissions": [
                          {
                            "databaseName": "db1"
                          }
                        ],
                        "settings": {
                          "readonly": "1"
                        }
                      }
                    }'
        ```

- gRPC API {#grpc-api}

    1. [Get an IAM token for API authentication](../api-ref/authentication.md) and save it as an environment variable:

        {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

    1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

    1. Make a request using {{ api-examples.grpc.tool }}:

        ```bash
        grpcurl \
            -format json \
            -import-path ~/cloudapi/ \
            -import-path ~/cloudapi/third_party/googleapis/ \
            -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/user_service.proto \
            -rpc-header "Authorization: Bearer $IAM_TOKEN" \
            -d '{
                  "cluster_id": "{{ cluster-id }}",
                  "user_spec": {
                    "name": "ro-user",
                    "password": "Passw0rd",
                    "permissions": [
                      {
                        "database_name": "db1"
                      }
                    ],
                    "settings": {
                      "readonly": "1"
                    }
                  }
                }' \
            {{ api-host-mdb }}:{{ port-https }} \
            yandex.cloud.mdb.clickhouse.v1.UserService.Create
        ```

- SQL {#sql}

  1. [Connect](connect/clients.md) to the `mych` cluster as [`admin`](#sql-user-management).
  1. Create a user:

      ```sql
      CREATE USER ro-user IDENTIFIED WITH sha256_password BY 'Passw0rd';
      ```

  1. Grant the user read permissions for all objects in the `db1` database:

      ```sql
      GRANT SELECT ON db1.* TO ro-user;
      ```

{% endlist %}

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}

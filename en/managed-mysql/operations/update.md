---
title: How to change {{ MY }} cluster settings in {{ mmy-full-name }}
description: In this tutorial, you will learn how to change settings for a {{ MY }} cluster.
---

# Updating {{ MY }} cluster settings

After creating a cluster, you can:

* [Change the host class](#change-resource-preset).

* [{#T}](#change-disk-size).

* [Changing {{ MY }} settings](#change-mysql-config).

    {% note warning %}

    You cannot change {{ MY }} settings using SQL commands.

    {% endnote %}

* [Change additional cluster settings](#change-additional-settings).

* [Move a cluster](#move-cluster) to another folder.


* [{#T}](#change-sg-set).


Learn more about other cluster updates:

* [{#T}](cluster-version-update.md).

* [Migrating cluster hosts to a different availability zone](host-migration.md).

## Changing the host class {#change-resource-preset}

The choice of a host class in {{ mmy-short-name }} clusters is limited by the CPU and RAM quotas available to DB clusters in your cloud. To check the resources currently in use, open the [Quotas]({{ link-console-quotas }}) page and find **{{ ui-key.yacloud.iam.folder.dashboard.label_mdb }}**.

{% include [mmy-settings-dependence](../../_includes/mdb/mmy/note-info-settings-dependence.md) %}

When changing the host class:

* Your single-host cluster will be unavailable for a few minutes with database connections terminated.
* Your multi-host cluster will get a new master host. Its hosts will be stopped and updated one by one. Once stopped, a host will be unavailable for a few minutes.
* Using a [special FQDN](./connect.md#special-fqdns) does not guarantee a stable database connection: user sessions may be terminated.

We recommend changing the host class only when the cluster has no active workload.

{% list tabs group=instructions %}

- Management console {#console}

  1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mysql }}**.
  1. Select the cluster and click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** in the top panel.
  1. To change the class of {{ MY }} hosts, under **{{ ui-key.yacloud.mdb.forms.section_resource }}**, select the required class.
  1. Click **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To change the [host class](../concepts/instance-types.md) for the cluster:

  1. View a description of the update cluster CLI command:

      ```bash
      {{ yc-mdb-my }} cluster update --help
      ```

  1. Request a list of available host classes (the `ZONE IDS` column specifies the availability zones where you can select the appropriate class):


     ```bash
     {{ yc-mdb-my }} resource-preset list
     ```

     Result:

     ```text
     +-----------+--------------------------------+-------+----------+
     |    ID     |            ZONE IDS            | CORES |  MEMORY  |
     +-----------+--------------------------------+-------+----------+
     | s1.micro  | {{ region-id }}-a, {{ region-id }}-b,  |     2 | 8.0 GB   |
     |           | {{ region-id }}-d                  |       |          |
     | ...                                                           |
     +-----------+--------------------------------+-------+----------+
     ```


  1. Specify the class in the update cluster command:

      ```bash
      {{ yc-mdb-my }} cluster update <cluster_name_or_ID>
        --resource-preset <class_ID>
      ```

      {{ mmy-short-name }} will run the update host class command for the cluster.

- {{ TF }} {#tf}

  1. Open the current {{ TF }} configuration file with an infrastructure plan.

      For more information about creating this file, see [Creating clusters](./cluster-create.md).

  1. In the {{ mmy-name }} cluster description, change the `resource_preset_id` parameter value under `resources`:

      ```hcl
      resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
        ...
        resources {
          resource_preset_id = "<host_class>"
          ...
        }
      }
      ```

  1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Confirm updating the resources.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    For more information, see the [{{ TF }} provider documentation]({{ tf-provider-mmy }}).

    {% include [Terraform timeouts](../../_includes/mdb/mmy/terraform/timeouts.md) %}

- API {#api}

    To change the [host class](../concepts/instance-types.md), use the [update](../api-ref/Cluster/update.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Update](../api-ref/grpc/Cluster/update.md) gRPC API call and provide the following in the request:

    * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md).
    * Required host class in the `configSpec.resources.resourcePresetId` parameter. To request a [list](../api-ref/ResourcePreset/list.md) of supported values, use the list method for the `ResourcePreset` resources.
    * List of settings to update (in this case, `configSpec.resources.resourcePresetId`), in the `updateMask` parameter.

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Increasing storage size {#change-disk-size}

{% include [note-increase-disk-size](../../_includes/mdb/note-increase-disk-size.md) %}

{% list tabs group=instructions %}

- Management console {#console}

  To increase the cluster storage size:

  1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mysql }}**.
  1. Select the cluster and click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** in the top panel.
  1. Under **{{ ui-key.yacloud.mdb.forms.section_disk }}**, specify the required value.
  1. Click **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To increase the cluster storage size:

  1. View a description of the update cluster CLI command:

      ```bash
      {{ yc-mdb-my }} cluster update --help
      ```

  1. Specify the required storage in the cluster update command (it must be at least as large as `disk_size` in the cluster properties):

      ```bash
      {{ yc-mdb-my }} cluster update <cluster_name_or_ID> \
        --disk-size <storage_size_in_GB>
      ```

- {{ TF }} {#tf}

  To increase the cluster storage size:

  1. Open the current {{ TF }} configuration file with an infrastructure plan.

      For more information about creating this file, see [Creating clusters](./cluster-create.md).

  1. Under `resources`, change the `disk_size` parameter value:

      ```hcl
      resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
        ...
        resources {
          disk_size = <storage_size_in_GB>
          ...
        }
      }
      ```

  1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Confirm updating the resources.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

  For more information, see the [{{ TF }} provider documentation]({{ tf-provider-mmy }}).

  {% include [Terraform timeouts](../../_includes/mdb/mmy/terraform/timeouts.md) %}

- API {#api}

    To increase the cluster storage size, use the [update](../api-ref/Cluster/update.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Update](../api-ref/grpc/Cluster/update.md) gRPC API call and provide the following in the request:

    * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md).
    * Storage size in the `configSpec.resources.diskSize` parameter.
    * List of updatable cluster configuration fields in the `updateMask` parameter (in this case, `configSpec.resources.diskSize`).

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Changing {{ MY }} settings {#change-mysql-config}

{% include [mmy-settings-dependence](../../_includes/mdb/mmy/note-info-settings-dependence.md) %}

{% list tabs group=instructions %}

- Management console {#console}

  1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mysql }}**.
  1. Select the cluster and click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** in the top panel.
  1. Configure the [{{ MY }} settings](../concepts/settings-list.md#dbms-cluster-settings) by clicking **{{ ui-key.yacloud.mdb.forms.button_configure-settings }}** under **{{ ui-key.yacloud.mdb.forms.section_settings }}**.
  1. Click **{{ ui-key.yacloud.component.mdb.settings.popup_settings-submit }}**.
  1. Click **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To update the [{{ MY }} settings](../concepts/settings-list.md#dbms-cluster-settings):

  1. View a description of the update cluster configuration CLI command:

      ```bash
      {{ yc-mdb-my }} cluster update-config --help
      ```

  1. Set the required parameter values.

     All supported parameters are listed in the [request format for the update method](../api-ref/Cluster/update.md), in the `mysql_config_5_7` field. To specify a parameter name in the CLI call, convert its name from <q>lowerCamelCase</q> to <q>snake_case</q>. For example, convert the `logMinDurationStatement` parameter from an API request to `log_min_duration_statement` for the CLI command:

     ```bash
     {{ yc-mdb-my }} cluster update-config <cluster_name>
       --set log_min_duration_statement=100,<parameter_name>=<value>,...
     ```

     {{ mmy-short-name }} runs the update cluster settings operation.

- {{ TF }} {#tf}

  1. Open the current {{ TF }} configuration file with an infrastructure plan.

      For more information about creating this file, see [Creating clusters](./cluster-create.md).

  1. In the {{ mmy-name }} cluster description, add or update the [DBMS settings](../concepts/settings-list.md) under `mysql_config`:

      ```hcl
      resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
        ...
        mysql_config = {
          <{{ MY }}_setting_name> = <value>
          ...
        }
      }
      ```

  1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Confirm updating the resources.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

  For more information, see the [{{ TF }} provider documentation]({{ tf-provider-resources-link }}/mdb_mysql_cluster#mysql-config).

  {% include [Terraform timeouts](../../_includes/mdb/mmy/terraform/timeouts.md) %}

- API {#api}

    To change {{ MY }} settings, use the [update](../api-ref/Cluster/update.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Update](../api-ref/grpc/Cluster/update.md) gRPC API call and provide the following in the request:

    * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md).
    * Array with new {{ MY }} settings in the following parameter:
        * `configSpec.mysqlConfig_5_7.sqlMode` for {{ MY }} version 5.7.
        * `configSpec.mysqlConfig_8_0.sqlMode` for {{ MY }} version 8.0.
    * List of cluster configuration fields to update in the `updateMask` parameter.

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

For more information on how to update the {{ MY }} settings, see [FAQ](../qa/configuring.md).

## Changing additional cluster settings {#change-additional-settings}

{% list tabs group=instructions %}

- Management console {#console}

  1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mysql }}**.
  1. Select the cluster and click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** in the top panel.
  1. Change additional cluster settings:

     {% include [mmy-extra-settings](../../_includes/mdb/mmy-extra-settings-web-console.md) %}

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To change additional cluster settings:

    1. View a description of the update cluster CLI command:

        ```bash
        {{ yc-mdb-my }} cluster update --help
        ```

    1. Run the following command with a list of settings to update:

        ```bash
        {{ yc-mdb-my }} cluster update <cluster_name_or_ID> \
          --backup-window-start <backup_start_time> \
          --backup-retain-period-days=<backup_retention_period> \
          --datalens-access=<true_or_false> \
          --websql-access=<true_or_false> \
          --maintenance-window type=<maintenance_type>,`
                              `day=<day_of_week>,`
                              `hour=<hour> \
          --deletion-protection \
          --performance-diagnostics enabled=true,`
                                   `sessions-sampling-interval=<session_sampling_interval>,`
                                   `statements-sampling-interval=<statement_sampling_interval>
        ```

    You can change the following settings:

    {% include [backup-window-start](../../_includes/mdb/cli/backup-window-start.md) %}

    * `--backup-retain-period-days`: Automatic backup retention period, in days. The possible values range from `7` to `60`. Default value: `7`.

    * `--datalens-access`: Enables access from {{ datalens-name }}. Default value: `false`. For more information about setting up a connection, see [{#T}](datalens-connect.md).

    * `--websql-access`: Enables [SQL queries](web-sql-query.md) against cluster databases from the {{ yandex-cloud }} management console using {{ websql-full-name }}. Default value: `false`.

    * `--maintenance-window`: [Maintenance window](../concepts/maintenance.md) settings (including for disabled clusters), where `type` is the maintenance type:

        {% include [maintenance-window](../../_includes/mdb/cli/maintenance-window-description.md) %}

    * {% include [Deletion protection](../../_includes/mdb/cli/deletion-protection.md) %}

        {% include [deletion-protection-limits-db](../../_includes/mdb/deletion-protection-limits-db.md) %}

    * `performance-diagnostics`: Enabling statistics collection for [cluster performance diagnostics](performance-diagnostics.md). For `sessions-sampling-interval` and `statements-sampling-interval`, possible values range from `1` to `86400` seconds.

    You can [get the cluster name with a list of clusters in the folder](cluster-list.md#list-clusters).

- {{ TF }} {#tf}

  1. Open the current {{ TF }} configuration file with an infrastructure plan.

      For more information about creating this file, see [Creating clusters](cluster-create.md).

  1. To change the backup start time, add a `backup_window_start` section to the {{ mmy-name }} cluster description:

      ```hcl
      resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
        ...
        backup_window_start {
          hours   = <hour>
          minutes = <minute>
        }
        ...
      }
      ```

      Where:

      * `hours`: Backup start hour.
      * `minutes`: Backup start minute.

  1. To change the backup retention period, specify the `backup_retain_period_days` parameter in the cluster description:

      ```hcl
        resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
          ...
          backup_retain_period_days = <backup_retention_period>
          ...
        }
      ```

      Where `backup_retain_period_days` is the automatic backup retention period, in days. The possible values range from `7` to `60`. Default value: `7`.

  1. {% include [Access settings](../../_includes/mdb/mmy/terraform/access-settings.md) %}

  1. {% include [Maintenance window](../../_includes/mdb/mmy/terraform/maintenance-window.md) %}

  1. To enable cluster protection against accidental deletion by a user of your cloud, add the `deletion_protection` field set to `true` to your cluster description:

      ```hcl
      resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
        ...
        deletion_protection = <deletion_protection>
      }
      ```

      Where `deletion_protection` is the cluster deletion protection, `true` or `false`.

      {% include [deletion-protection-limits-db](../../_includes/mdb/deletion-protection-limits-db.md) %}

  1. To enable statistics collection for [cluster performance diagnostics](performance-diagnostics.md), add the `performance_diagnostics` section to your {{ mmy-name }} cluster description:

      ```hcl
      resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
        ...
        performance_diagnostics {
          enabled = true
          sessions_sampling_interval = <session_sampling_interval>
          statements_sampling_interval = <statement_sampling_interval>
        }
        ...
      }
      ```

      For `sessions_sampling_interval` and `statements_sampling_interval`, possible values range from `1` to `86400` seconds.

  1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Confirm updating the resources.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

  For more information, see the [{{ TF }} provider documentation]({{ tf-provider-mmy }}).

  {% include [Terraform timeouts](../../_includes/mdb/mmy/terraform/timeouts.md) %}

- API {#api}

    To change additional cluster settings, use the [update](../api-ref/Cluster/update.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Update](../api-ref/grpc/Cluster/update.md) gRPC API call and provide the following in the request:

    * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md#list-clusters).
    * Settings for access from other services in the `configSpec.access` parameter.
    * Backup window settings in the `configSpec.backupWindowStart` parameter.
    * [Maintenance](../concepts/maintenance.md) window settings (including for disabled clusters) in the `maintenanceWindow` parameter.
    * Retention period of automatic backups in the `configSpec.backupRetainPeriodDays` parameter. The possible values range from `7` to `60`. Default value: `7`.
    * Cluster deletion protection settings in the `deletionProtection` parameter.

      {% include [deletion-protection-limits-db](../../_includes/mdb/deletion-protection-limits-db.md) %}

    * Settings of statistics collection for [cluster performance diagnostics](performance-diagnostics.md) in the `configSpec.performanceDiagnostics` parameter.

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

    You can get the cluster ID with a [list of clusters in the folder](./cluster-list.md#list-clusters).

{% endlist %}

## Moving a cluster {#move-cluster}

{% list tabs group=instructions %}

- Management console {#console}

    1. Go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mysql }}**.
    1. Click ![image](../../_assets/console-icons/ellipsis.svg) to the right of the cluster you want to move.
    1. Select **{{ ui-key.yacloud.mdb.dialogs.popup_button_move-cluster }}**.
    1. Select a folder you want to move the cluster to.
    1. Click **{{ ui-key.yacloud.mdb.dialogs.popup_button_move-cluster }}**.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To move a cluster:

    1. View a description of the CLI move cluster command:

        ```bash
        {{ yc-mdb-my }} cluster move --help
        ```

    1. Specify the destination folder in the move cluster command:

        ```bash
        {{ yc-mdb-my }} cluster move <cluster_ID> \
           --destination-folder-name=<destination_folder_name>
        ```

        You can get the cluster ID with a [list of clusters in the folder](cluster-list.md#list-clusters).

- {{ TF }} {#tf}

    1. Open the current {{ TF }} configuration file with an infrastructure plan.

        For more information about creating this file, see [Creating clusters](./cluster-create.md).

    1. In the {{ mmy-name }} cluster description, edit or add the `folder_id` parameter value:

        ```hcl
        resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
          ...
          folder_id = "<destination_folder_ID>"
        }
        ```

    1. Make sure the settings are correct.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Confirm updating the resources.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    For more information, see the [{{ TF }} provider documentation]({{ tf-provider-mmy }}).

    {% include [Terraform timeouts](../../_includes/mdb/mmy/terraform/timeouts.md) %}

- API {#api}

    To move a cluster, use the [move](../api-ref/Cluster/move.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Move](../api-ref/grpc/Cluster/move.md) gRPC API call and provide the following in the request:

    * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md#list-clusters).
    * ID of the destination folder in the `destinationFolderId` parameter.

{% endlist %}


## Changing security groups {#change-sg-set}

{% list tabs group=instructions %}

- Management console {#console}

    1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mysql }}**.
    1. Select the cluster and click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** in the top panel.
    1. Under **{{ ui-key.yacloud.mdb.forms.section_network }}**, select security groups for cluster network traffic.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To edit the list of [security groups](../concepts/network.md#security-groups) for your cluster:

    1. View a description of the update cluster CLI command:

        ```bash
        {{ yc-mdb-my }} cluster update --help
        ```

    1. Specify the security groups in the update cluster command:

        ```bash
        {{ yc-mdb-my }} cluster update <cluster_name_or_ID> \
          --security-group-ids <list_of_security_group_IDs>
        ```

- {{ TF }} {#tf}

  1. Open the current {{ TF }} configuration file with an infrastructure plan.

      For more information about creating this file, see [Creating clusters](./cluster-create.md).

  1. In the {{ mmy-name }} cluster description, change the `security_group_ids` parameter value:

      ```hcl
      resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
        ...
        security_group_ids = [<list_of_security_group_IDs>]
      }
      ```

  1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Confirm updating the resources.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

  For more information, see the [{{ TF }} provider documentation]({{ tf-provider-mmy }}).

  {% include [Terraform timeouts](../../_includes/mdb/mmy/terraform/timeouts.md) %}

- API {#api}

    To edit the list of cluster security groups, use the [update](../api-ref/Cluster/update.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Update](../api-ref/grpc/Cluster/update.md) gRPC API call and provide the following in the request:

    * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](./cluster-list.md#list-clusters).
    * List of security group IDs in the `securityGroupIds` parameter.
    * List of settings to update (in this case, `securityGroupIds`), in the `updateMask` parameter.

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

{% note warning %}

You may need to additionally [set up security groups](connect.md#configure-security-groups) to connect to the cluster.

{% endnote %}


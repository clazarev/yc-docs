# Implementing a secure high-availability network infrastructure with a dedicated DMZ based on the UserGate NGFW


Follow this tutorial to deploy a secure network infrastructure based on the [UserGate next-generation firewall](https://www.usergate.com/products/next-generation-firewall). The infrastructure is made up of segments, each containing single-purpose resources, isolated from other resources. For example, the [DMZ](https://en.wikipedia.org/wiki/DMZ_(computing)) segment is reserved for public-facing applications, whereas the `mgmt` segment contains infrastructure management resources. Each segment will have its own cloud folder and a dedicated {{ vpc-short-name }} [cloud network](../../vpc/concepts/network.md#network). The segments communicate with each other via a [next-generation firewall (NGFW)](https://en.wikipedia.org/wiki/Next-generation_firewall) VM, which provides end-to-end protection and traffic control across the segments.

You can see the solution architecture in the diagram below.

![image](../../_assets/tutorials/high-accessible-dmz-usergate.png)

The solution comprises these main segments (folders):

* The **public** folder contains the internet-facing resources.
* The **mgmt** folder is for cloud infrastructure management and internal resources. It includes two VMs for infrastructure protection and network segmentation into security zones (`fw-a` and `fw-b`) and a VM with [WireGuard VPN](https://www.wireguard.com/) configured for secure access to the management segment (jump-vm).
* **dmz** that enables you to publish open-access applications.

For more information, see the [project repository](https://github.com/yandex-cloud-examples/yc-dmz-with-high-available-usergate-ngfw).

To deploy a secure high-availability network infrastructure with a dedicated DMZ based on the UserGate next-generation firewall:

1. [Get your cloud ready](#prepare-cloud).
1. [Set up your environment](#prepare-environment).
1. [Deploy your resources](#create-resources).
1. [Configure the NGFW](#configure-ngfw).
1. [Enable the route switcher](#enable-route-switcher).
1. [Test the solution for performance and fault tolerance](#test-accessibility).
1. [Requirements for production deployment](#deployment-requirements).

If you no longer need the resources you created, [delete them](#clear-out).

### Next-Generation Firewall {#next-generation-firewall}

An NGFW is used for cloud network protection and segmentation with a dedicated DMZ for public-facing applications.

[{{ marketplace-full-name }}]({{ link-cloud-marketplace }}?categories=security) offers multiple NGFW solutions. This scenario uses [UserGate NGFW]({{ link-cloud-marketplace }}/products/usergate/ngfw). Its features include:
* Firewalling.
* Intrusion detection and prevention.
* Traffic management and internet access control.
* Content filtering and application control.
* VPN server.
* Stream-based antivirus.
* Protection against DoS attacks and network flooding.

In this tutorial, we use the UserGate NGFW configuration with basic firewall and NAT rules.

Learn more about what UserGate NGFW can do in the [vendor’s documentation](https://www.usergate.com/ru/products/usergate-vm).

## Get your cloud ready {#prepare-cloud}

{% include [before-you-begin](../../_tutorials/_tutorials_includes/before-you-begin.md) %}

### Required paid resources {#paid-resources}

The infrastructure support cost includes:

* Fee for continuously running VMs (see [{{ compute-full-name }} pricing](../../compute/pricing.md)).
* Fee for using {{ alb-name }} (see [{{ alb-full-name }} pricing](../../application-load-balancer/pricing.md)).
* Fee for using {{ network-load-balancer-name }} (see [{{ network-load-balancer-full-name }} pricing](../../network-load-balancer/pricing.md)).
* Fee for using public IP addresses and outgoing traffic (see [{{ vpc-full-name }} pricing](../../vpc/pricing.md)).
* Fee for using functions (see [{{ sf-full-name }} pricing](../../functions/pricing.md)).
* Fee for using [UserGate NGFW](/marketplace/products/usergate/ngfw).

### Required quotas {#required-quotes}

{% note warning %}

In this tutorial, you will deploy a resource-intensive infrastructure.

{% endnote %}

Make sure you have sufficient cloud [quotas](../../overview/concepts/quotas-limits.md) not used by other projects.

{% cut "Resources used by this tutorial" %}

   | Resource | Quantity |
   | ----------- | ----------- |
   | Folders | 3 |
   | Instance groups | 1 |
   | Virtual machines | 5 |
   | VM vCPUs | 14 |
   | VM RAM | 38 GB |
   | Disks | 5 |
   | SSD size | 400 GB |
   | HDD size | 30 GB |
   | Networks | 3 |
   | Subnets | 6 |
   | Route tables | 2 |
   | Security groups | 6 |
   | Static public IP addresses | 4 |
   | Public IP addresses | 4 |
   | Static routes | 5 |
   | Buckets | 1 |
   | Cloud functions | 1 |
   | Cloud function triggers | 1 |
   | Total RAM for all running functions | 128 MB |
   | Network load balancers (NLB) | 2 |
   | NLB target groups | 2 |
   | Application load balancers (ALB) | 1 |
   | ALB backend groups | 1 |
   | ALB target groups | 1 |

{% endcut %}

## Set up your environment {#prepare-environment}

### Create a cloud administrator service account {#create-account}
   
{% list tabs group=instructions %}

- Management console {#console}

   1. In the [management console]({{ link-console-main }}), select the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) where you want to create your service account.
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_iam }}**.
   1. Click **{{ ui-key.yacloud.iam.folder.service-accounts.button_add }}**.
   1. Name your service account, e.g., `sa-terraform`.

       The naming requirements are as follows:

       {% include [name-format](../../_includes/name-format.md) %}

       {% include [sa-uniq-name](../../_includes/iam/sa-uniq-name.md) %}

   1. Click **{{ ui-key.yacloud.iam.folder.service-account.popup-robot_button_add }}**.

   1. Assign the admin [role](../../iam/concepts/access-control/roles.md) to the service account:

       1. On the management console [home page]({{ link-console-main }}), select your cloud.
       1. Navigate to the **{{ ui-key.yacloud.common.resource-acl.label_access-bindings }}** tab.
       1. Click **{{ ui-key.yacloud.common.resource-acl.button_configure-access }}**.
       1. In the window that opens, click **{{ ui-key.yacloud_components.acl.label.service-accounts}}** and select the `sa-terraform` service account.
       1. Click ![image](../../_assets/console-icons/plus.svg) **{{ ui-key.yacloud_components.acl.button.add-role }}** and select the `admin` role.
       1. Click **{{ ui-key.yacloud_components.acl.action.apply }}**.

- CLI {#cli}

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   1. Create a service account:

         ```bash
         yc iam service-account create --name sa-terraform
         ```

         Where `name` is the service account name. The naming requirements are as follows:

         {% include [name-format](../../_includes/name-format.md) %}

         Result:

         ```text
         id: ajehr0to1g8b********
         folder_id: b1gv87ssvu49********
         created_at: "2024-01-04T09:03:11.665153755Z"
         name: sa-terraform
         ```
      
   1. Assign the admin [role](../../iam/concepts/access-control/roles.md) to the account:

         ```bash
         yc resource-manager cloud add-access-binding <cloud_ID> \
           --role admin \
           --subject serviceAccount:<service_account_ID>
         ```

         Result:

         ```text
         done (1s)
         ```

- API {#api}

   To create a service account, use the [create](../../iam/api-ref/ServiceAccount/create.md) REST API method for the [ServiceAccount](../../iam/api-ref/ServiceAccount/index.md) resource or the [ServiceAccountService/Create](../../iam/api-ref/grpc/ServiceAccount/create.md) gRPC API call.

   {% include [grant-role-for-sa-to-folder-via-api](../../_includes/iam/grant-role-for-sa-to-folder-via-api.md) %}

{% endlist %}

### Install the required tools {#install-utilities}

1. Install [Git](https://en.wikipedia.org/wiki/Git) using the following command:

   ```bash
   sudo apt install git
   ```

1. Install {{ TF }}:

   1. Navigate to the root directory:

      ```bash
      cd ~
      ```

   1. Create the `terraform` directory and open it:
   
      ```bash
      mkdir terraform
      cd terraform
      ```

   1. Download the `terraform_1.9.5_linux_amd64.zip` file:

      ```bash
      curl \
        --location \
        --remote-name \
        https://hashicorp-releases.yandexcloud.net/terraform/1.9.5/terraform_1.9.5_linux_amd64.zip
      ```

   1. Install `zip` and unpack the ZIP archive:
      
      ```bash
      apt install zip
      unzip terraform_1.9.5_linux_amd64.zip
      ```

   1. Add the path to the directory with the executable to the `PATH` variable: 
      
      ```bash
      export PATH=$PATH:~/terraform
      ```

   1. Make sure {{ TF }} is installed by running this command:
   
      ```bash
      terraform -help
      ```

1. Create a configuration file specifying the {{ TF }} provider source:

   1. Create the `.terraformrc` file in `nano`:

      ```bash
      cd ~
      nano .terraformrc
      ```

   1. Add the following section to the file:

      ```text
      provider_installation {
        network_mirror {
          url = "https://terraform-mirror.yandexcloud.net/"
          include = ["registry.terraform.io/*/*"]
        }
        direct {
          exclude = ["registry.terraform.io/*/*"]
        }
      }
      ```

      For more information about mirror settings, see the relevant [{{ TF }} guides](https://www.terraform.io/cli/config/config-file#explicit-installation-method-configuration).

## Deploy your resources {#create-resources}

1. Clone the GitHub [repository](https://github.com/yandex-cloud-examples/yc-dmz-with-high-available-usergate-ngfw) and navigate to the `yc-dmz-with-high-available-usergate-ngfw` script directory:
   
   ```bash
   git clone https://github.com/yandex-cloud-examples/yc-dmz-with-high-available-usergate-ngfw.git
   cd yc-dmz-with-high-available-usergate-ngfw
   ```

1. Set up the CLI profile to run operations under the service account:

   {% list tabs group=instructions %}

   - CLI {#cli}

      {% include [cli-install](../../_includes/cli-install.md) %}

      {% include [default-catalogue](../../_includes/default-catalogue.md) %}

      1. Create an [authorized key](../../iam/concepts/authorization/key.md) for your service account and save it to the file:
         
         ```bash
         yc iam key create \
           --service-account-id <service_account_ID> \
           --folder-id <ID_of_folder_with_service_account> \
           --output key.json
         ```

         Where:
         
         * `service-account-id`: Service account ID.
         * `folder-id`: ID of the folder where you created the service account.
         * `output`: Authorized key file name.

         Result:
         
         ```text
         id: aje8nn871qo4********
         service_account_id: ajehr0to1g8b********
         created_at: "2023-03-04T09:16:43.479156798Z"
         key_algorithm: RSA_2048
         ```

      1. Create a CLI profile to run operations under the service account:
        
         ```bash
         yc config profile create sa-terraform
         ```

         Result:
         
         ```text
         Profile 'sa-terraform' created and activated
         ```

      1. Configure the profile:
         
         ```bash
         yc config set service-account-key key.json
         yc config set cloud-id <cloud_ID>
         yc config set folder-id <folder_ID>
         ```

         Where:

         * `service-account-key`: Service account authorized key file.
         * `cloud-id`: [Cloud](../../resource-manager/operations/cloud/get-id.md) ID.
         * `folder-id`: [Folder ID](../../resource-manager/operations/folder/get-id.md).

      1. Add your credentials to the environment variables:
         
         ```bash
         export YC_TOKEN=$(yc iam create-token)
         ```

   {% endlist %}

1. Get your PC's IP address:
      
   ```bash
   curl 2ip.ru
   ```

   Result:
   
   ```text
   192.2**.**.**
   ```

1. Open the `terraform.tfvars` file in `nano` and edit the following:

   1. Cloud ID line:
      
      ```text
      cloud_id = "<cloud_ID>"
      ```

   1. Line with a list of public IP addresses allowed to access `jump-vm`:
      
      ```text
      trusted_ip_for_access_jump-vm = ["<external_IP_address_of_your_PC>/32"]
      ```

   {% cut "`terraform.tfvars` variable description" %}

   | Parameter<br>name | Change<br>required | Description | Type | Example |
   | ----------- | ----------- | ----------- | ----------- | ----------- |
   | `cloud_id` | Yes | Your Yandex Cloud ID | `string` | `b1g8dn6s3v2e********` |
   | `az_name_list` | - | List of two Yandex Cloud <a href="../../overview/concepts/geo-scope">availability zones</a> to host your resources | `list(string)` | `["{{ region-id }}-a", "{{ region-id }}-b"]` |
   | `security_segment_names` | - | Segment names. The first segment is for management resources, the second, for internet-facing resources, and the third, for DMZ. If you need more segments, add them at the end of the list. When adding a segment, make sure to specify the subnet prefixes in `zone1_subnet_prefix_list` and `zone2_subnet_prefix_list`. | `list(string)` |  `["mgmt", "public", "dmz"]` |
   | `zone1_subnet_prefix_list` | - | List of network prefixes in the first availability zone for subnets mapped to the `security_segment_names` list. Specify one prefix for each segment from the `security_segment_names` list. | `list(string)` | `["192.168.1.0/24", "172.16.1.0/24", "10.160.1.0/24"]` |
   | `zone2_subnet_prefix_list` | - | List of network prefixes in the second availability zone for subnets mapped to the `security_segment_names` list. Specify one prefix for each segment from the `security_segment_names` list. | `list(string)` | `["192.168.2.0/24", "172.16.2.0/24", "10.160.2.0/24"]` |
   | `public_app_port` | - | TCP port for a DMZ application open for internet connection | `number` | `80` |
   | `internal_app_port` | - | DMZ application internal TCP port receiving traffic from NGFW. You may specify the same port as `public_app_port` or a different one. | `number` | `8080` |
   | `trusted_ip_for_access_jump-vm` | Yes | List of public IP addresses/subnets allowed to access the jump VM. It is used in the incoming rule of the jump VM security group. | `list(string)` | `["A.A.A.A/32", "B.B.B.0/24"]` |
   | `jump_vm_admin_username` | - | Jump VM username for SSH connections. | `string` | `admin` |
   | `wg_port` | - | Jump VM WireGuard inbound UDP port. | `number` | `51820` |
   | `wg_client_dns` | - | List of DNS server addresses in the management cloud network the admin workstation will use after establishing a WireGuard tunnel to the jump VM. | `string` | `192.168.1.2, 192.168.2.2` |

   {% endcut %}

1. Deploy your cloud resources using {{ TF }}:

   1. Initialize {{ TF }}:
       
      ```bash
      terraform init
      ```

   1. Check the {{ TF }} file configuration:
       
      ```bash
      terraform validate
      ```

   1. Check the list of new cloud resources:
       
      ```bash
      terraform plan
      ```

   1. Create the resources:
       
      ```bash
      terraform apply
      ```

1. Once the process is completed, you will see the list of created resources. You can also display this list with the `terraform output` command:

   {% cut "Expand to view the deployed resource details" %}

   | Name | Description | Value (example) |
   | ----------- | ----------- | ----------- |
   | `dmz-web-server-nlb_ip_address` | IP address of the load balancer in the DMZ segment downstream of which there is a target group of web servers to test publishing an application from the DMZ. configuring destination NAT on the firewall. | `"10.160.1.100"` |
   | `fw-a_ip_address` | FW-A IP address in the management network | `"192.168.1.10"` |
   | `fw-alb_public_ip_address` | ALB public IP address. It is used to access an application published in the DMZ from the internet. | `"C.C.C.C"` |
   | `fw-b_ip_address` | FW-B IP address in the management network | `"192.168.2.10"` |
   | `jump-vm_path_for_WireGuard_client_config` | Configuration file for a secure WireGuard VPN connection to the jump VM | `"./jump-vm-wg.conf"` |
   | `jump-vm_public_ip_address_jump-vm` | Jump VM public IP address | `"D.D.D.D"` |
   | `path_for_private_ssh_key` | File with a private key for connection to VMs over SSH (jump-vm, FW-A, FW-B, DMZ web servers) | `"./pt_key.pem"` |
   
   {% endcut %}

## Configure the NGFW {#configure-ngfw}

This tutorial describes how to configure firewalls named FW-A and FW-B with the basic firewall and NAT rules required to test performance and fault tolerance in our scenario but insufficient for [production deployment](#deployment-requirements).

### Connect to the management segment via a VPN {#connect-via-vpn}

After deploying the infrastructure, the `mgmt` folder will contain the `jump-vm` Ubuntu instance with the configured [WireGuard VPN](https://www.wireguard.com/) providing secure connection. Set up a VPN tunnel to `jump-vm` on your PC to access the `mgmt`, `dmz`, and `public` segment subnets.

You can also connect to the jump VM over SSH using the SSH key from `terraform output` and the username from the `jump_vm_admin_username` variable.

To set up a VPN tunnel:

1. [Install](https://www.wireguard.com/install/) WireGuard on your PC.
1. Open WireGuard and click **Add Tunnel**.
1. In the dialog that opens, select the `jump-vm-wg.conf` file in the `yc-dmz-with-high-available-usergate-ngfw` directory.

1. Click **Activate** to activate the tunnel.
1. Check network connectivity with the management server via the WireGuard VPN tunnel by running the following command in the terminal:
   
   ```bash
   ping 192.168.1.101
   ```

   {% note warning %}

   If the packets fail to reach the management server, make sure the `mgmt-jump-vm-sg` [security group](../../vpc/concepts/security-groups.md) rules for incoming traffic have your PC external IP address specified correctly.

   {% endnote %}

### Configure the FW-A firewall {#configure-fw-a}

Connect to the FW-A management web interface at `https://192.168.1.10:8001`. Use the default admin credentials: `Admin` for both username and password. After connecting, the system will prompt you to change your password.

#### Configure your network {#configure-fw-a-network}

1. In the top menu, go to **Settings**, and in the left-hand menu, under **UserGate**, select **Settings**. Click the **Time zone** field value. Select your time zone and click **Save**. In the **Primary NTP server** and **Backup NTP server** fields, enter the addresses of the NTP servers (see the list of recommended NTP servers [here](../../tutorials/infrastructure-management/ntp.md)).

1. In the left-hand menu, in the **Network** section, select **Interfaces**. Click `port0`. In the **Network** tab, select `Mode: Static`. Make sure the interface IP address is `192.168.1.10`. Click **Save**.

1. Click `port1`. On the **General** tab, check **Enabled**. Select `Zone: Untrusted`. In the **Network** tab, select `Mode: DHCP`. Click **Save**. Make sure the interface has been assigned the `172.16.1.10` IP address via DHCP. Click `port1` once more. In the **Network** tab, select `Mode: Static` and click **Save**.

1. Click `port2`. On the **General** tab, check **Enabled**. Select `Zone: DMZ`. In the **Network** tab, select `Mode: DHCP`. Click **Save**. Make sure the interface has been assigned the `10.160.1.10` IP address via DHCP. Click `port2` once more. In the **Network** tab, select `Mode: Static` and click **Save**.

1. In the left-hand menu, in the **Network** section, select **Virtual Routers**. Click `—` (em dash) in the **Static routes** column for **Default virtual router**. Click **Add** to add the static routes from the table: 

   | Name | Enabled | Destination address | Gateway |
   | --- | --- | --- | --- |
   | `route to mgmt-zone2` | ![image](../../_assets/common/yes.svg) | `192.168.2.0/24` | `192.168.1.1` | 
   | `route to dmz-zone2` | ![image](../../_assets/common/yes.svg) | `10.160.2.0/24` | `10.160.1.1` |
   | `route to nlb-healthcheck-net1` | ![image](../../_assets/common/yes.svg) | `198.18.235.0/24` | `192.168.1.1` |
   | `route to nlb-healthcheck-net2` | ![image](../../_assets/common/yes.svg) | `198.18.248.0/24` | `192.168.1.1` |

1. In the left-hand menu, in the **Network** section, select **Gateways**. Select the row with the `192.168.1.1` gateway IP address. To delete the gateway, click **Delete** and confirm the deletion. Click **Add**. Fill out the fields as follows: 
   * **Name**: `public-gateway`
   * **Interface**: `port1`
   * **Gateway IP address**: `172.16.1.1`
  
   Enable **Default** and click **Save**.

1. In the left-hand menu, in the **Network** section, select **DNS**. Under **System DNS servers **, add the `192.168.1.2` IP address of the cloud DNS server in the `mgmt` segment.

#### Diagnostics for basic settings {#diagnostics-base-settings}

1. In the top menu, go to **Diagnostics and monitoring** and select **Routes** in the left-hand menu under **Monitoring**. Make sure the output of the routing information includes the static routes you added and the default gateway.

   ```text
   VRF default:
   K>* 0.0.0.0/0 [0/0] via 172.16.1.1, port1, 00:03:54
   K>* 10.160.2.0/24 [0/0] via 10.160.1.1, port2, 00:04:57
   K>* 192.168.2.0/24 [0/0] via 192.168.1.1, port0, 00:04:57
   K>* 198.18.235.0/24 [0/0] via 192.168.1.1, port0, 00:04:57
   K>* 198.18.248.0/24 [0/0] via 192.168.1.1, port0, 00:04:57
   ```

1. In the left-hand menu, select **DNS request** in the **Network** section. In the **DNS request (host)** field, enter the internet domain name of the resource, e.g., `ya.ru`. In the **Request source IP address** field, select `192.168.1.10`. Click **Start** and make sure the domain name resolves to public IP addresses.

1. In the left-hand menu, select **Ping** in the **Network** section. In the **Ping host** field, enter the internet domain name of the resource, e.g., `ya.ru`. Select `port1` for **Interface**. Click **Start** and make sure the ping is successful. In the **Ping host** field, enter the IP address of the other firewall in the `mgmt` segment. Select `port0` for **Interface**. Click **Start** and make sure the ping is successful.

   ```
   --- ya.ru ping statistics ---
   6 packets transmitted, 6 received, 0% packet loss, time 5006ms
   rtt min/avg/max/mdev = 3.381/3.468/3.813/0.172 ms
   ```

{% note info %}

   The default port for connecting to UserGate over SSH is `2200`:

   ```bash
   ssh -i pt_key.pem Admin@192.168.1.10 -p 2200
   ```

   To learn more about managing UserGate via the command line interface, see the relevant [documentation](https://docs.usergate.com/interfejs-komandnoj-stroki-(cli)_91.html).

{% endnote %}

#### Updating software and libraries {#update-software}

Optionally, you can update your UserGate version. 

1. In the top menu, go to **Settings**, and in the left-hand menu, under **UserGate**, select **Settings**. In **Update download schedule**, click **Check for updates**. In the window that opens, in the **Software update** tab, click **Check for updates**. If updates are available, you can download them.

1. In the **Library update** tab, click **Check for updates**. If updates are available, you can download them.

1. Once the updates are downloaded, navigate to the **UserGate** section in the left-hand menu and select **Device management**. Under **Server operations**, in **Server updates**, click **Install now**. Confirm installing the updates. During the update, the firewall will reboot.

#### Configuring basic security policies {#configure-base-security-fw-a}

1. In the top menu, go to **Settings** and select **Zones** in the left-hand menu under **Network**. Click the **Management** zone and go to the **Access control** tab. Make sure the ![image](../../_assets/common/yes.svg) option is enabled for **Administration console**. Click **Any** in the same row under **Allowed addresses**. Add the subnets allowed to access the UserGate administration console. Click **Add**. Enter `192.168.1.0/24` and click **Save**. Add the `192.168.2.0/24` subnet in the same way. Then, click **Save** in the **Select IP address/subnet** window.

1. For the **Management** zone, add the allowed `192.168.1.0/24` and `192.168.2.0/24` addresses to **CLI over SSH** in the same way to enable SSH access for managing UserGate.

1. For the **Management** zone, add the allowed `198.18.235.0/24` and `198.18.248.0/24` addresses to **Captive portal and block page** in the same way. These addresses are used by the `route-switcher-lb-...` network load balancer of the `route-switcher` module to check the UserGate availability.

1. In the left-hand menu, under **Libraries**, select **IP addresses**. In the **Groups** section, add groups. For each group, under **Addresses from the selected group**, add IP addresses according to this table:

   | Name | Threat level | Addresses from the selected group |
   | ----------- | ----------- | ----------- |
   | `mgmt` | Medium | `192.168.1.0/24`<br>`192.168.2.0/24` |
   | `dmz` | Medium | `10.160.1.0/24`<br>`10.160.2.0/24` |
   | `FW-a-public-IP` | Medium | `172.16.1.10` |
   | `dmz-web-server` | Medium | `10.160.1.100` |

1. In the left-hand menu, under **Libraries**, select **Services** and click **Add**. In the **Name** field, specify `TCP_8080` and click **Add**. Select **tcp** as **Network protocol** and set **Destination ports** to `8080`. Click **Save** twice.

1. In the left-hand menu, under **Network policies**, select **NAT and routing**. Add a NAT rule to enable internet access from the `dmz` segment. In this case, the query request packet headers from the `dmz` segment to the internet will be translated to the source IP of the firewall interface in the `public` segment. Click **Add** and fill in the following fields in the **General** tab:
   * **Name**: `DMZ to internet`.
   * **Type**: Select `NAT` from the list.
   * **SNAT IP**: `172.16.1.10`.
   * **Logging**: Select `Log session start` from the list.

1. Switch to the **Source** tab and select the `DMZ` source zone. Under **Source address**, click **Add** and select **Add IP address list**. Select the `dmz` IP list.

1. Switch to the **Destination** tab and select the **Untrusted** destination zone. Leave the **Destination address** section empty to use any public IP address as the destination. In the **Rule properties** window, click **Save** to complete the NAT rule setup.

1. Add a destination NAT rule to route user requests to the traffic load balancer in the `dmz` segment, which distributes requests across a group of web servers hosting the test application. When configuring this rule, add source NAT to ensure the app response returns through the same firewall that processed the user request. Headers of packets received from {{ alb-name }} with user requests to the application published in `dmz` will be translated to the source IP of the firewall `dmz` interface and the destination IP of the web server traffic load balancer. Click **Add** and fill in the following fields in the **General** tab:
   * **Name**: `Internet to dmz-web-server`.
   * **Type**: Select `DNAT` from the list.
   * **SNAT IP**: `10.160.1.10`.
   * **Logging**: Select `Log session start` from the list.

1. Switch to the **Source** tab and select the **Untrusted** source zone. Leave the **Source address** section empty to use any IP address as the source.

1. Switch to the **Destination** tab. Under **Destination address**, click **Add** and select **Add IP address list**. Select the `FW-a-public-IP` IP list.

1. Switch to the **Service** tab and click **Add**. Select `TCP_8080` from the list, click **Add** and then **Close**. 

1. Switch to the **DNAT** tab. In the **DNAT destination address** field, enter `10.160.1.100`. Check **Enable SNAT**. In the **Rule properties** window, click **Save** to complete adding a DNAT rule.

   {% note info %}

      NAT rules are applied in the order they are listed, from top to bottom. Only the first rule for which all the conditions are met will apply. Which means, the more specific rules should be higher on the list than the more general ones.

   {% endnote %}

1. In the left-hand menu, under **Network policies**, select **Firewall** to add firewall rules. Click **Add** and fill in the following fields in the **General** tab:
   * **Name**: `Web-server port forwarding on FW-a`.
   * **Action**: Select `Allow` from the list.
   * **Logging**: Select `Log session start` from the list.

1. Switch to the **Source** tab and select the **Untrusted** source zone. Leave the **Source address** section empty to use any IP address as the source.

1. Switch to the **Destination** tab and select the **DMZ** destination zone. Under **Destination address**, click **Add** and select **Add IP address list**. Select the `dmz-web-server` IP list.

1. Switch to the **Service** tab and click **Add**. Select `TCP_8080` from the list, click **Add** and then **Close**. In the **Firewall rule properties** window, click **Save** to complete the rule setup.

1. Add the remaining rules from the table below to complete the configuration example with basic rules for testing firewall policies, publishing a test application from the `dmz` segment, and testing its fault tolerance. Note that you do not need to recreate the `Web-server port forwarding on FW-a` rule. 

   | # | Name | Action | Logging | Source zone | Source address | Destination zone | Destination address | Service |
   | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
   | 1 | `Web-server port forwarding on FW-a` | Allow | Log session start | `Untrusted` | Any | `DMZ` | `dmz-web-server` | `TCP_8080` |
   | 2 | `Mgmt to DMZ` | Allow | Log session start | `Management` | `mgmt` | `DMZ` | `dmz` | Any |
   | 3 | `Ping from dmz to internet` | Allow | Log session start | `DMZ` | `dmz` | `Untrusted` | Any | `Any ICMP` |
   | 4 | `Block all` | Deny | No | Any | Any | Any | Any | Any |

   {% note info %}

      Rules are processed one by one in the order they are listed, from top to bottom. Only the first rule for which all the conditions are met will apply. Which means, the more specific rules should be higher on the list than the more general ones. The `Block all` rule is used to prohibit any transit traffic through UserGate and should be placed at the end of the list. This is a required rule since the default `Default block` rule does not block traffic allowed by a DNAT rule.

   {% endnote %}

### Configure the FW-B firewall {#configure-fw-b}

Connect to the FW-B management web interface at `https://192.168.2.10:8001`. Use the default admin credentials: `Admin` for both username and password. After connecting, the system will prompt you to change your password.

#### Configure your network {#configure-fw-b-network}

1. In the top menu, go to **Settings**, and in the left-hand menu, under **UserGate**, select **Settings**. Click the **Time zone** field value. Select your time zone and click **Save**. In the **Primary NTP server** and **Backup NTP server** fields, enter the addresses of the NTP servers (see the list of recommended NTP servers [here](../../tutorials/infrastructure-management/ntp.md)).

1. In the left-hand menu, in the **Network** section, select **Interfaces**. Click `port0`. In the **Network** tab, select `Mode: Static`. Make sure the interface IP address is `192.168.2.10`. Click **Save**.

1. Click `port1`. On the **General** tab, check **Enabled**. Select `Zone: Untrusted`. In the **Network** tab, select `Mode: DHCP`. Click **Save**. Make sure the interface has been assigned the `172.16.2.10` IP address via DHCP. Click `port1` once more. In the **Network** tab, select `Mode: Static` and click **Save**.

1. Click `port2`. On the **General** tab, check **Enabled**. Select `Zone: DMZ`. In the **Network** tab, select `Mode: DHCP`. Click **Save**. Make sure the interface has been assigned the `10.160.2.10` IP address via DHCP. Click `port2` once more. In the **Network** tab, select `Mode: Static` and click **Save**.

1. In the left-hand menu, in the **Network** section, select **Virtual Routers**. Click `—` (em dash) in the **Static routes** column for **Default virtual router**. Click **Add** to add the static routes from the table: 

   | Name | Enabled | Destination address | Gateway |
   | --- | --- | --- | --- |
   | `route to mgmt-zone1` | ![image](../../_assets/common/yes.svg) | `192.168.1.0/24` | `192.168.2.1` | 
   | `route to dmz-zone1` | ![image](../../_assets/common/yes.svg) | `10.160.1.0/24` | `10.160.2.1` |
   | `route to nlb-healthcheck-net1` | ![image](../../_assets/common/yes.svg) | `198.18.235.0/24` | `192.168.2.1` |
   | `route to nlb-healthcheck-net2` | ![image](../../_assets/common/yes.svg) | `198.18.248.0/24` | `192.168.2.1` |

1. In the left-hand menu, in the **Network** section, select **Gateways**. Select the row with the `192.168.2.1` gateway IP address. To delete the gateway, click **Delete** and confirm the deletion. Click **Add**. Fill out the fields as follows: 
   * **Name**: `public-gateway`
   * **Interface**: `port1`
   * **Gateway IP address**: `172.16.2.1`
  
   Enable **Default** and click **Save**.

1. In the left-hand menu, in the **Network** section, select **DNS**. Under **System DNS servers **, add the `192.168.2.2` IP address of the cloud DNS server in the `mgmt` segment.

#### Diagnostics for settings and software updates {#diagnostics-update-fw-b}

1. [Check that the basic settings are applied correctly](#diagnostics-base-settings), as you did for FW-A.

1. You can also [update](#update-software) your UserGate version on FW-B. 

#### Configuring basic security policies {#configure-base-security-fw-b}

1. In the top menu, go to **Settings** and select **Zones** in the left-hand menu under **Network**. Click the **Management** zone and go to the **Access control** tab. Make sure the ![image](../../_assets/common/yes.svg) option is enabled for **Administration console**. Click **Any** in the same row under **Allowed addresses**. Add the subnets allowed to access the UserGate administration console. Click **Add**. Enter `192.168.1.0/24` and click **Save**. Add the `192.168.2.0/24` subnet in the same way. Then, click **Save** in the **Select IP address/subnet** window.

1. For the **Management** zone, add the allowed `192.168.1.0/24` and `192.168.2.0/24` addresses to **CLI over SSH** in the same way to enable SSH access for managing UserGate.

1. For the **Management** zone, add the allowed `198.18.235.0/24` and `198.18.248.0/24` addresses to **Captive portal and block page** in the same way. These addresses are used by the `route-switcher-lb-...` network load balancer of the `route-switcher` module to check the UserGate availability.

1. In the left-hand menu, under **Libraries**, select **IP addresses**. In the **Groups** section, add groups. For each group, under **Addresses from the selected group**, add IP addresses according to this table:

   | Name | Threat level | Addresses from the selected group |
   | ----------- | ----------- | ----------- |
   | `mgmt` | Medium | `192.168.1.0/24`<br>`192.168.2.0/24` |
   | `dmz` | Medium | `10.160.1.0/24`<br>`10.160.2.0/24` |
   | `FW-b-public-IP` | Medium | `172.16.2.10` |
   | `dmz-web-server` | Medium | `10.160.1.100` |

1. In the left-hand menu, under **Libraries**, select **Services** and click **Add**. In the **Name** field, specify `TCP_8080` and click **Add**. Select **tcp** as **Network protocol** and set **Destination ports** to `8080`. Click **Save** twice.

1. In the left-hand menu, under **Network policies**, select **NAT and routing**. Add a NAT rule to enable internet access from the `dmz` segment. In this case, the query request packet headers from the `dmz` segment to the internet will be translated to the source IP of the firewall interface in the `public` segment. Click **Add** and fill in the following fields in the **General** tab:
   * **Name**: `DMZ to internet`.
   * **Type**: Select `NAT` from the list.
   * **SNAT IP**: `172.16.2.10`
   * **Logging**: Select `Log session start` from the list.

1. Switch to the **Source** tab and select the `DMZ` source zone. Under **Source address**, click **Add** and select **Add IP address list**. Select the `dmz` IP list.

1. Switch to the **Destination** tab and select the **Untrusted** destination zone. Leave the **Destination address** section empty to use any public IP address as the destination. In the **Rule properties** window, click **Save** to complete the NAT rule setup.

1. Add a destination NAT rule to route user requests to the traffic load balancer in the `dmz` segment, which distributes requests across a group of web servers hosting the test application. Click **Add** and fill in the following fields in the **General** tab:
   * **Name**: `Internet to dmz-web-server`.
   * **Type**: Select `DNAT` from the list.
   * **SNAT IP**: `10.160.2.10`
   * **Logging**: Select `Log session start` from the list.

1. Switch to the **Source** tab and select the **Untrusted** source zone. Leave the **Source address** section empty to use any IP address as the source.

1. Switch to the **Destination** tab. Under **Destination address**, click **Add** and select **Add IP address list**. Select the `FW-b-public-IP` IP list.

1. Switch to the **Service** tab and click **Add**. Select `TCP_8080` from the list, click **Add** and then **Close**. 

1. Switch to the **DNAT** tab. In the **DNAT destination address** field, enter `10.160.1.100`. Check **Enable SNAT**. In the **Rule properties** window, click **Save** to complete adding a DNAT rule.

1. In the left-hand menu, under **Network policies**, select **Firewall** to add firewall rules. Click **Add** and fill in the following fields in the **General** tab:
   * **Name**: `Web-server port forwarding on FW-b`.
   * **Action**: Select `Allow` from the list.
   * **Logging**: Select `Log session start` from the list.

1. Switch to the **Source** tab and select the **Untrusted** source zone. Leave the **Source address** section empty to use any IP address as the source.

1. Switch to the **Destination** tab and select the **DMZ** destination zone. Under **Destination address**, click **Add** and select **Add IP address list**. Select the `dmz-web-server` IP list.

1. Switch to the **Service** tab and click **Add**. Select `TCP_8080` from the list, click **Add** and then **Close**. In the **Firewall rule properties** window, click **Save** to complete the rule setup.

1. Add the remaining rules from the table below to complete the configuration example with basic rules for testing firewall policies, publishing a test application from the `dmz` segment, and testing its fault tolerance. Note that you do not need to recreate the `Web-server port forwarding on FW-b` rule. 

   | # | Name | Action | Logging | Source zone | Source address | Destination zone | Destination address | Service |
   | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
   | 1 | `Web-server port forwarding on FW-b` | Allow | Log session start | `Untrusted` | Any | `DMZ` | `dmz-web-server` | `TCP_8080` |
   | 2 | `Mgmt to DMZ` | Allow | Log session start | `Management` | `mgmt` | `DMZ` | `dmz` | Any |
   | 3 | `Ping from dmz to internet` | Allow | Log session start | `DMZ` | `dmz` | `Untrusted` | Any | `Any ICMP` |
   | 4 | `Block all` | Deny | No | Any | Any | Any | Any | Any |

## Enable the route switcher {#enable-route-switcher}

After you complete the NGFW setup, make sure FW-A and FW-B health checks return `Healthy`. To do this, in the {{ yandex-cloud }} [management console]({{ link-console-main }}), navigate to the `mgmt` folder, select **{{ network-load-balancer-name }}**, and go to the `route-switcher-lb-...` page. Expand the target group and make sure the targets are `Healthy`. If they are `Unhealthy`, check that FW-A and FW-B are up and running and properly [configured](#configure-gateways).

Once FW-A and FW-B get the `Healthy` status, change the `route-switcher` module's `start_module` value to `true` in the `route-switcher.tf` file. To enable the module, run this command:

```bash
terraform plan
terraform apply
```

Within five minutes, the `route-switcher` module will start working, providing outbound traffic fault tolerance.

## Test the solution for performance and fault tolerance {#test-accessibility}

### Test the system {#test-accessibility}

1. To get the load balancer public IP address, run this command in the terminal:

   ```bash
   terraform output fw-alb_public_ip_address
   ```

1. Make sure your network infrastructure is accessible from outside by opening the following address in your browser:
    
   ```text
   http://<ALB_load_balancer_public_IP_address>
   ```
   You should see the `Welcome to nginx!` page.

1. Make sure the firewall security policy rules that allow traffic are active. To do this, go to the `yc-dmz-with-high-available-usergate-ngfw` folder on your PC and connect to a VM in the DMZ segment over SSH:

   ```bash
   cd yc-dmz-with-high-available-usergate-ngfw
   ssh -i pt_key.pem admin@<VM_internal_IP_address_in_DMZ_segment>
   ```

1. To check whether the DMZ-hosted VM has internet access, run this command:
   
   ```bash
   ping ya.ru
   ```

   The command must run according to the `ping from dmz to internet` rule that allows traffic.

1. Connect to the FW-A management web interface at `https://192.168.1.10:8001`. In the top menu, go to **Settings** and select **Firewall** in the left-hand menu under **Network policies**. Configure logging for the `Block all` rule: `Log session start`. 

1. Make sure the security policy rules that prohibit traffic are applied.

   To check that `Jump VM` in the `mgmt` segment cannot be accessed from the `dmz` segment, run this command: 

   ```bash
   ping 192.168.1.101
   ```
   The command should end with an error according to the `Block all` rule.

1. Connect to the FW-A management web interface at `https://192.168.1.10:8001`. In the top menu, go to **Logs and reports** and select **Traffic log** in the left-hand menu under **Logs**. In the `Rules:` filter, select `Block all` and `ping from dmz to internet`. Make sure the logs include records of allowed and blocked traffic for the tests performed. After that, disable logging for the `Block all` rule. 

### Testing fault tolerance {#fault-tolerance-check}

1. Install `httping` for making HTTP requests on your PC:

   ```bash
   sudo apt-get install httping
   ```

1. To get the load balancer public IP address, run this command in the terminal:

   ```bash
   terraform output fw-alb_public_ip_address
   ```

1. Initiate DMZ application inbound traffic by making a request to the ALB public IP address:

   ```bash
   httping http://<ALB_load_balancer_public_IP_address>
   ```

1. Open another terminal window and connect to a DMZ VM over SSH:
   
   ```bash
   ssh -i pt_key.pem admin@<VM_internal_IP_address_in_DMZ_segment>
   ```

1. Set a password for the `admin` user:

   ```bash
   sudo passwd admin
   ```

1. In the {{ yandex-cloud }} [management console]({{ link-console-main }}), change the settings of this VM:

   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
   1. In the left-hand panel, select ![image](../../_assets/console-icons/server.svg) **{{ ui-key.yacloud.compute.instances_jsoza }}**.
   1. Click ![ellipsis](../../_assets/console-icons/ellipsis.svg) next to the VM you need and select ![pencil](../../_assets/console-icons/pencil.svg) **{{ ui-key.yacloud.common.edit }}**.
   1. In the window that opens, under **{{ ui-key.yacloud.compute.instances.create.section_additional }}**, enable **{{ ui-key.yacloud.compute.instance.overview.field_serial-port-enable }}**.
   1. Click **{{ ui-key.yacloud.compute.instance.edit.button_update }}**.
   
1. Connect to the VM serial console, enter the `admin` username and the password you set earlier.

1. Initiate outbound traffic from the DMZ VM to an internet resource by running `ping`:

   ```bash
   ping ya.ru
   ```

1. Emulate the main firewall failure by [stopping](../../compute/operations/vm-control/vm-stop-and-start.md#stop) the `FW-A` VM in the `mgmt` folder of the {{ yandex-cloud }} [management console]({{ link-console-main }}).
1. Monitor the loss of `httping` and `ping` packets. After FW-A fails, you may experience traffic loss for about one minute, then traffic should resume.
1. Make sure the `dmz-rt` route table in the `dmz` folder uses the `FW-B` address as `next hop`.
1. Emulate the main firewall recovery by [running](../../compute/operations/vm-control/vm-stop-and-start.md#start) the `FW-A` VM in the {{ yandex-cloud }} [management console]({{ link-console-main }}). 
1. Monitor the loss of `httping` and `ping` packets. After FW-A recovers, you may experience traffic loss for about one minute, then traffic should resume.
1. Make sure the `dmz-rt` route table uses the FW-A address for `next hop` in the `dmz` folder.

## Production deployment requirements {#deployment-requirements}

* Save the `pt_key.pem` private SSH key to a secure location or recreate it separately from {{ TF }}.
* Delete the public IP address of the jump VM if you are not going to use it.
* If your plan is to use it for connection to the management segment via WireGuard VPN, change the WireGuard keys both on the jump VM and admin workstation.
* Configure the UserGate NGFW to meet your specific needs in line with the corporate security policy.
* Do not assign public IP addresses to the VMs in those segments where the UserGate NGFW routing tables with a default route of `0.0.0.0/0` are used (learn more [here](../../vpc/concepts/routing.md#restrictions)). The only exception is the `mgmt` segment where routing tables do not use the `0.0.0.0/0` default route. 

## How to delete the resources you created {#clear-out}

To stop paying for the resources you created, run this command:

   ```bash
   terraform destroy
   ```

   {% note warning %}

   {{ TF }} will **permanently** delete all resources, such as networks, subnets, VMs, load balancers, folders, etc.

   {% endnote %}

As the resources you created reside in folders, a faster way to delete all resources is to delete all the folders using the {{ yandex-cloud }} [management console]({{ link-console-main }}) and then delete the `terraform.tfstate` file from the `yc-dmz-with-high-available-usergate-ngfw` folder on your PC.
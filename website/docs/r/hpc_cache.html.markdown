---
subcategory: "Storage"
layout: "azurerm"
page_title: "Azure Resource Manager: azurerm_hpc_cache"
description: |-
  Manages a HPC Cache.
---

# azurerm_hpc_cache

Manages a HPC Cache.

~> **Note**: During the first several months of the GA release, a request must be made to the Azure HPC Cache team to add your subscription to the access list before it can be used to create a cache instance. Fill out [this form](https://aka.ms/onboard-hpc-cache) to request access.

~> **NOTE:**: By request of the service team the provider no longer automatically registering the `Microsoft.StorageCache` Resource Provider for this resource. To register it you can run `az provider register --namespace 'Microsoft.StorageCache'`.

## Example Usage

```hcl
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_virtual_network" "example" {
  name                = "examplevn"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

resource "azurerm_subnet" "example" {
  name                 = "examplesubnet"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.1.0/24"]
}

resource "azurerm_hpc_cache" "example" {
  name                = "examplehpccache"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  cache_size_in_gb    = 3072
  subnet_id           = azurerm_subnet.example.id
  sku_name            = "Standard_2G"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the HPC Cache. Changing this forces a new resource to be created.

* `resource_group_name` - (Required) The name of the Resource Group in which to create the HPC Cache. Changing this forces a new resource to be created.

* `location` - (Required) Specifies the supported Azure Region where the HPC Cache should be created. Changing this forces a new resource to be created.

* `cache_size_in_gb` - (Required) The size of the HPC Cache, in GB. Possible values are `3072`, `6144`, `12288`, `24576`, and `49152`. Changing this forces a new resource to be created.

* `subnet_id` - (Required) The ID of the Subnet for the HPC Cache. Changing this forces a new resource to be created.

* `sku_name` - (Required) The SKU of HPC Cache to use. Possible values are `Standard_2G`, `Standard_4G` and `Standard_8G`. Changing this forces a new resource to be created.

---

* `mtu` - (Optional) The IPv4 maximum transmission unit configured for the subnet of the HPC Cache. Possible values range from 576 - 1500. Defaults to 1500.

* `default_access_policy` - (Optional) A `default_access_policy` block as defined below.

* `ntp_server` - (Optional) The NTP server IP Address or FQDN for the HPC Cache. Defaults to `time.windows.com`.

* `dns` - (Optional) A `dns` block as defined below.
  
---

An `access_rule` block contains the following:

* `scope` - (Required) The scope of this rule. The `scope` and (potentially) the `filter` determine which clients match the rule. Possible values are: `default`, `network`, `host`.

~> **NOTE**: Each `access_rule` should set a unique `scope`.

* `access` - (Required) The access level for this rule. Possible values are: `rw`, `ro`, `no`.

* `filter` - (Optional) The filter applied to the `scope` for this rule. The filter's format depends on its scope: `default` scope matches all clients and has no filter value; `network` scope takes a CIDR format; `host` takes an IP address or fully qualified domain name. If a client does not match any filter rule and there is no default rule, access is denied.

* `suid_enabled` - (Optional) Whether [SUID](https://docs.microsoft.com/en-us/azure/hpc-cache/access-policies#suid) is allowed? Defaults to `false`.

* `submount_access_enabled` - (Optional) Whether allow access to subdirectories under the root export? Defaults to `false`.

* `root_squash_enabled` - (Optional) Whether to enable [root squash](https://docs.microsoft.com/en-us/azure/hpc-cache/access-policies#root-squash)? Defaults to `false`.

* `anonymous_uid` - (Optional) The anonymous UID used when `root_squash_enabled` is `true`.
  
* `anonymous_gid` - (Optional) The anonymous GID used when `root_squash_enabled` is `true`.

---

A `default_access_policy` block contains the following:

* `access_rule` - (Required) One to three `access_rule` blocks as defined above.

---

A `dns` block contains the following:

* `servers` - (Required) A list of DNS servers for the HPC Cache. At most three IP(s) are allowed to set.

* `search_domain` - (Optional) The DNS search domain for the HPC Cache.

## Attributes Reference

The following attributes are exported:

* `id` - The `id` of the HPC Cache.

* `mount_addresses` - A list of IP Addresses where the HPC Cache can be mounted.

## Timeouts

The `timeouts` block allows you to specify [timeouts](https://www.terraform.io/docs/configuration/resources.html#timeouts) for certain actions:

* `create` - (Defaults to 30 minutes) Used when creating the HPC Cache.
* `read` - (Defaults to 5 minutes) Used when retrieving the HPC Cache.
* `delete` - (Defaults to 30 minutes) Used when deleting the HPC Cache.

## Import

HPC Caches can be imported using the `resource id`, e.g.

```shell
terraform import azurerm_hpc_cache.example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.StorageCache/caches/cacheName
```

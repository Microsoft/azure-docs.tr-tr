---
title: Öğretici-Terrayform kullanarak Azure 'da bir bağlı ağ oluşturma
description: Hub-kol topolojisinde hub 'a bağlı iki bağlı bileşen sanal ağı uygulamayı öğrenin
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 2a36b8ac22fb52f6b8f1246fd254d9c3ff22fc82
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159015"
---
# <a name="tutorial-create-a-spoke-network-in-azure-using-terraform"></a>Öğretici: Terrayform kullanarak Azure 'da bir bağlı ağ oluşturma

Bu öğreticide, iş yüklerinin ayrılmasını göstermek için iki ayrı bağlı ağa sahip olursunuz. Ağlar, hub sanal ağını kullanarak ortak kaynakları paylaşır. Uçlar, iş yüklerini diğer uçlardan ayrı olarak yönetilen kendi sanal ağlarında yalıtmak için kullanılabilir. Her iş yükü birden fazla katman içerebilir. Birden fazla alt ağ, Azure yük dengeleyicileri aracılığıyla birbirine bağlanır.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Hub-ışınsal-uç topolojisinde bağlı olan sanal ağları uygulamak için HCL (HashiCorp Language) kullanın
> * Bağlı ağlardaki sanal makineler oluşturmak için Terrayform kullanma
> * Sanal ağ eşayarlarını hub ağlarla oluşturmak için Terrayform kullanın

## <a name="prerequisites"></a>Önkoşullar

1. [Azure 'Da Terrayform ile bir hub ve bağlı bileşen karma ağ topolojisi oluşturun](./terraform-hub-spoke-introduction.md).
1. [Azure 'Da Terrayform ile şirket içi sanal ağ oluşturun](./terraform-hub-spoke-on-prem.md).
1. [Azure 'Da Terrayform ile bir hub sanal ağı oluşturun](./terraform-hub-spoke-hub-network.md).
1. [Azure 'Da Terrayform ile bir hub sanal ağ gereci oluşturun](./terraform-hub-spoke-hub-nva.md).

## <a name="create-the-directory-structure"></a>Dizin yapısını oluşturma

Bu bölümde iki bağlı bileşen komut dosyası oluşturulur. Her betik, bir bağlı ağ sanal ağını ve iş yükü için bir sanal makineyi tanımlar. Hub 'dan bağlı ağa eşlenen bir sanal ağ daha sonra oluşturulur.

1. [Azure portala](https://portal.azure.com) gidin.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)'i açın. Önceden bir ortam seçmediyseniz **Bash** ortamını seçin.

    ![Cloud Shell istemi](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. `clouddrive` dizinine geçin.

    ```bash
    cd clouddrive
    ```

1. Dizinleri yeni dizinle değiştirin:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-two-spoke-networks"></a>İki bağlı ağ ağını bildirme

1. Cloud Shell ' de, `spoke1.tf`adlı yeni bir dosya açın.

    ```bash
    code spoke1.tf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırın:

    ```hcl
    locals {
      spoke1-location       = "CentralUS"
      spoke1-resource-group = "spoke1-vnet-rg"
      prefix-spoke1         = "spoke1"
    }

    resource "azurerm_resource_group" "spoke1-vnet-rg" {
      name     = local.spoke1-resource-group
      location = local.spoke1-location
    }

    resource "azurerm_virtual_network" "spoke1-vnet" {
      name                = "spoke1-vnet"
      location            = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke1-vnet-rg.name
      address_space       = ["10.1.0.0/16"]

      tags {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_subnet" "spoke1-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefix       = "10.1.0.64/27"
    }

    resource "azurerm_subnet" "spoke1-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefix       = "10.1.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke1-hub-peer" {
      name                      = "spoke1-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke1-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet" , "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke1-nic" {
      name                 = "${local.prefix-spoke1}-nic"
      location             = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke1
        subnet_id                     = azurerm_subnet.spoke1-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "spoke1-vm" {
      name                  = "${local.prefix-spoke1}-vm"
      location              = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke1-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke1-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke1}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke1-peer" {
      name                      = "hub-spoke1-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke1-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```

1. Dosyayı kaydedin ve düzenleyiciden çıkın.

1. `spoke2.tf` adlı yeni bir dosya oluşturun.

      ```bash
      code spoke2.tf
      ```
    
1. Aşağıdaki kodu düzenleyiciye yapıştırın:
    
    ```hcl
    locals {
      spoke2-location       = "CentralUS"
      spoke2-resource-group = "spoke2-vnet-rg"
      prefix-spoke2         = "spoke2"
    }

    resource "azurerm_resource_group" "spoke2-vnet-rg" {
      name     = local.spoke2-resource-group
      location = local.spoke2-location
    }

    resource "azurerm_virtual_network" "spoke2-vnet" {
      name                = "${local.prefix-spoke2}-vnet"
      location            = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke2-vnet-rg.name
      address_space       = ["10.2.0.0/16"]

      tags {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_subnet" "spoke2-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefix       = "10.2.0.64/27"
    }

    resource "azurerm_subnet" "spoke2-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefix       = "10.2.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke2-hub-peer" {
      name                      = "${local.prefix-spoke2}-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke2-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke2-nic" {
      name                 = "${local.prefix-spoke2}-nic"
      location             = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke2
        subnet_id                     = azurerm_subnet.spoke2-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_machine" "spoke2-vm" {
      name                  = "${local.prefix-spoke2}-vm"
      location              = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke2-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke2-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke2}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke2-peer" {
      name                      = "hub-spoke2-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke2-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```
     
1. Dosyayı kaydedin ve düzenleyiciden çıkın.
  
## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure 'da Terrayform ile bir hub ve bağlı bileşen ağı doğrulama](./terraform-hub-spoke-validation.md)
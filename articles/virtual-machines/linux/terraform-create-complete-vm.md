---
title: Terrayform kullanarak Azure 'da tamamen bir Linux sanal makinesi oluşturun
description: Azure 'da tüm Linux sanal makine ortamı oluşturmak ve yönetmek için Terrayform kullanmayı öğrenin
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: b97d9563f0bddcc2b3bf82807f41af68f3abbff1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034711"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Terrayform ile Azure 'da tamamen bir Linux sanal makine altyapısı oluşturma

Terrayform, Azure 'da tüm altyapı dağıtımlarını tanımlamanızı ve oluşturmanızı sağlar. Azure kaynaklarını tutarlı, tekrarlanabilir bir şekilde oluşturup yapılandıran, Kullanıcı tarafından okunabilen bir biçimde Terrayform şablonları oluşturursunuz. Bu makalede, tüm Linux ortamının nasıl oluşturulduğu ve Terrayform ile destekleyici kaynakların nasıl oluşturulduğu gösterilir. Ayrıca, [Terrayform 'U yüklemeyi ve yapılandırmayı](terraform-install-configure.md)da öğrenebilirsiniz.


## <a name="create-azure-connection-and-resource-group"></a>Azure bağlantısı ve kaynak grubu oluşturma

Bir Terrayform şablonunun her bir bölümünü inceleyelim. Ayrıca, kopyalayabilir ve yapıştırabileceğiniz [Terrayform şablonunun](#complete-terraform-script) tam sürümünü de görebilirsiniz.

`provider` bölümü, Teraform 'un bir Azure sağlayıcısı kullanmasını söyler. *Subscription_id*, *client_id*, *client_secret*ve *Tenant_id*değerlerini almak için bkz. [terrayform 'u yüklemek ve yapılandırmak](terraform-install-configure.md). 

> [!TIP]
> Değerler için ortam değişkenleri oluşturursanız veya [Azure Cloud Shell Bash deneyimini](/azure/cloud-shell/overview) kullanıyorsanız, bu bölüme değişken bildirimleri eklemeniz gerekmez.

```hcl
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

Aşağıdaki bölümde `eastus` konumunda `myResourceGroup` adlı bir kaynak grubu oluşturulur:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

Ek bölümlerde, kaynak grubuna *$ {azurerm_resource_group. mytersformgroup. Name}* ile başvurmalısınız.

## <a name="create-virtual-network"></a>Sanal ağ oluşturma
Aşağıdaki bölüm, *10.0.0.0/16* adres alanında *myvnet* adlı bir sanal ağ oluşturur:

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

Aşağıdaki bölüm, *Myvnet* sanal ağında *mysubnet* adlı bir alt ağ oluşturur:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Genel IP adresi oluştur
Internet üzerindeki kaynaklara erişmek için, VM 'nize bir genel IP adresi oluşturun ve atayın. Aşağıdaki bölüm *Mypublicıp*adlı BIR genel IP adresi oluşturur:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Ağ güvenlik grubu oluştur
Ağ güvenlik grupları, sanal makinenizin içindeki ve dışı ağ trafiği akışını denetler. Aşağıdaki bölümde, *Mynetworksecuritygroup* adlı bir ağ güvenlik grubu oluşturulur ve TCP bağlantı noktası 22 ' de SSH trafiğine izin veren bir kural tanımlanmaktadır:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Sanal ağ arabirim kartı oluştur
Sanal ağ arabirim kartı (NIC), VM 'nizi belirli bir sanal ağ, genel IP adresi ve ağ güvenlik grubuna bağlar. Bir Terrayform şablonunda bulunan aşağıdaki bölüm, oluşturduğunuz sanal ağ kaynaklarına bağlı *MYNIC* adlı BIR sanal NIC oluşturur:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    network_security_group_id   = azurerm_network_security_group.myterraformnsg.id

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Tanılama için depolama hesabı oluşturma
Bir VM için önyükleme tanılamayı depolamak üzere bir depolama hesabı gerekir. Bu önyükleme Tanılaması, sorunları gidermenize ve sanal makinenizin durumunu izlemenize yardımcı olabilir. Oluşturduğunuz depolama hesabı yalnızca önyükleme tanılama verilerini depolamak içindir. Her depolama hesabının benzersiz bir adı olması gerektiği için aşağıdaki bölümde bazı rastgele metinler oluşturulur:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Artık bir depolama hesabı oluşturabilirsiniz. Aşağıdaki bölümde, önceki adımda oluşturulan rastgele metne göre ada sahip bir depolama hesabı oluşturulur:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Sanal makine oluşturma

Son adım, bir sanal makine oluşturmak ve oluşturulan tüm kaynakları kullanmaktır. Aşağıdaki bölüm, *Myvm* adlı bir VM oluşturur ve *MYNIC*adlı sanal NIC 'yi ekler. En son *Ubuntu 16,04-LTS* görüntüsü kullanılır ve parola kimlik doğrulaması devre dışı olarak *azureuser* adlı bir Kullanıcı oluşturulur.

 SSH anahtar verileri *ssh_keys* bölümünde verilmiştir. *Key_data* alanında geçerli BIR genel SSH anahtarı sağlayın.

```hcl
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Terrayform betiğini doldurun

Bu bölümlerin tümünü bir araya getirmek ve Terrampaform 'u görmek için *terraform_azure. tf* adlı bir dosya oluşturun ve aşağıdaki içeriği yapıştırın:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Altyapıyı derleme ve dağıtma
Terrayform şablonunuz oluşturulduktan sonra, birinci adım Terrayform 'u başlatmalıdır. Bu adım, Azure 'da şablonunuzu derlemek için Terrampaform 'un tüm önkoşullara sahip olmasını sağlar.

```bash
terraform init
```

Bir sonraki adım, Terrayform 'nun şablonu incelemesinin ve doğrulayacaktır. Bu adım, istenen kaynakları Terrayform tarafından kaydedilen durum bilgileriyle karşılaştırır ve ardından planlı yürütmeyi çıkartır. Kaynaklar Azure *'da oluşturulmaz.*

```bash
terraform plan
```

Önceki komutu yürütmeden sonra, aşağıdaki ekrana benzer bir şey görmeniz gerekir:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Her şey doğru görünüyorsa ve altyapıyı Azure 'da oluşturmaya hazırsanız, şablonu Terrayform 'da uygulayın:

```bash
terraform apply
```

Terrampaform tamamlandığında, VM altyapınız hazırlayın. [Az VM Show](/cli/azure/vm)ile sanal MAKINENIZIN genel IP adresini edinin:

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Daha sonra sanal makinenize SSH gönderebilirsiniz:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Sonraki adımlar
Azure 'da Terrayform kullanarak temel altyapı oluşturdunuz. Yük dengeleyiciler ve sanal makine ölçek kümeleri kullanan örnekler dahil daha karmaşık senaryolar için bkz. [Azure için çok sayıda Terkform örneği](https://github.com/hashicorp/terraform/tree/master/examples). Desteklenen Azure sağlayıcılarının güncel listesi için bkz. [Terrayform belgeleri](https://www.terraform.io/docs/providers/azurerm/index.html).

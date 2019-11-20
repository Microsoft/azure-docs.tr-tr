---
title: Öğretici-Terrayform ve HCL ile Azure VM kümesi oluşturma
description: Azure 'da yük dengeleyiciye sahip bir Linux sanal makine kümesi oluşturmak için Terrayform ve HCL kullanma
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: f28cbbf13015d07c9d789ed258a9e2b0582ba1da
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159259"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Öğretici: Terrayform ve HCL ile Azure VM kümesi oluşturma

Bu öğreticide, [HCL](https://www.terraform.io/docs/configuration/syntax.html)kullanarak küçük bir işlem kümesi oluşturmayı göreceksiniz. 

Aşağıdaki görevleri nasıl gerçekleştireceğinizi öğreneceksiniz:

> [!div class="checklist"]
> * Azure kimlik doğrulamasını ayarlayın.
> * Bir Terrayform yapılandırma dosyası oluşturun.
> * Bir yük dengeleyici oluşturmak için bir Terrayform yapılandırma dosyası kullanın.
> * Bir kullanılabilirlik kümesinde iki Linux VM dağıtmak için bir Terrayform yapılandırma dosyası kullanın.
> * Terraform'u başlatın.
> * Terrayform yürütme planı oluşturun.
> * Azure kaynaklarını oluşturmak için Terrayform yürütme planını uygulayın.

## <a name="1-set-up-azure-authentication"></a>1. Azure kimlik doğrulamasını ayarlama

> [!NOTE]
> [Terraform ortam değişkenlerini kullanıyorsanız](/azure/virtual-machines/linux/terraform-install-configure) veya bu [Azure Cloud Shell](terraform-cloud-shell.md) öğreticisini çalıştırdıysanız bu bölümü atlayın.

Bu bölümde bir Azure hizmet sorumlusu ve hizmet sorumlusu kimlik bilgilerini içeren iki Terraform yapılandırma dosyası oluşturacaksınız.

1. Terraform'un Azure'da kaynak sağlaması için bir [Azure AD hizmet sorumlusu ayarlayın](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure). Hizmet sorumlusunu oluştururken abonelik kimliği, kiracı, uygulama kimliği ve parola değerlerini not edin.

2. Bir komut istemi açın.

3. Terraform dosyalarınızı depolamak için boş bir dizin oluşturun.

4. Değişkenlerinizin bildirimleri için yeni bir dosya oluşturun. Bu dosyaya istediğiniz adı verebilirsiniz ancak sonuna `.tf` uzantısını eklemeniz gerekir.

5. Aşağıdaki kodu değişken bildirim dosyanıza kopyalayın:

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Terraform değişkenlerinin değerlerini için yeni bir dosya oluşturun. Terartform değişken dosyanızı, geçerli dizinde varsa, `terraform.tfvars` adlı herhangi bir dosyayı (veya `*.auto.tfvars`bir desen takip eden) otomatik olarak yüklediği `terraform.tfvars` adlandırma yaygındır. 

7. Aşağıdaki kodu değişken dosyanıza kopyalayın. Yer tutucuları şu şekilde değiştirdiğinizden emin olun: `subscription_id` için `az account set` komutunu çalıştırdığınızda belirttiğiniz Azure abonelik kimliğini kullanın. `tenant_id` için `tenant` komutunun döndürdüğü `az ad sp create-for-rbac` değerini kullanın. `client_id` için `appId` komutunun döndürdüğü `az ad sp create-for-rbac` değerini kullanın. `client_secret` için `password` komutunun döndürdüğü `az ad sp create-for-rbac` değerini kullanın.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. bir Terrayform yapılandırma dosyası oluşturma

Bu bölümde altyapınız için kaynak tanımlarını içeren dosyayı oluşturacaksınız.

1. `main.tf` adlı yeni bir dosya oluşturun. 

2. Aşağıdaki örnek kaynak tanımlarını yeni oluşturduğunuz `main.tf` dosyasına kopyalayın: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = [azurerm_lb_backend_address_pool.test.id]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. Terrayform 'u başlatma 

[terraform init komutu](https://www.terraform.io/docs/commands/init.html), önceki bölümlerde oluşturduğunuz Terraform yapılandırma dosyalarını içeren bir dizin başlatmak için kullanılır. Yeni bir tersform yapılandırması yazdıktan sonra her zaman `terraform init` komutunu çalıştırmak iyi bir uygulamadır. 

> [!TIP]
> `terraform init` komutu bir kere etkilidir ve tekrar tekrar çağrılarak aynı sonuç elde edilebilir. Bu nedenle bir işbirliğine dayalı bir ortamda çalışıyorsanız ve yapılandırma dosyalarınızın değiştirilmiş olabileceğini düşünüyorsanız planı yürütmeden veya uygulamadan önce `terraform init` komutunu çalıştırmanız yararlı olacaktır.

Terraform'u başlatmak için şu komutu çalıştırın:

  ```bash
  terraform init
  ```

  ![Terraform'u başlatma](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. bir Terrayform yürütme planı oluşturma

[terraform plan komutu](https://www.terraform.io/docs/commands/plan.html), yürütme planı oluşturmak için kullanılır. Terraform, yürütme planı oluşturmak için geçerli dizindeki tüm `.tf` dosyalarını toplar. 

[-Out parametresi](https://www.terraform.io/docs/commands/plan.html#out-path) , yürütme planını bir çıkış dosyasına kaydeder. Bu özellik, çok dev ortamlarında ortak olan eşzamanlılık sorunlarını giderir. Çıkış dosyası tarafından çözülen bir sorun aşağıdaki senaryodur:

1. Geliştirme 1, yapılandırma dosyasını oluşturur.
1. Geliştirme 2, yapılandırma dosyasını değiştirir.
1. Geliştirici 1 yapılandırma dosyasını uygular (çalıştırır).
1. Dev 1, Dev 2 ' nin yapılandırmayı değiştirmediğini bilmeyen beklenmedik sonuçlar alıyor.

Geliştirme 1 bir çıkış dosyası belirtildiğinde, Dev 2 ' nin dev 1 ' i etkilemesini önler. 

Yürütme planınızı kaydetmeniz gerekmiyorsa, aşağıdaki komutu çalıştırın:

  ```bash
  terraform plan
  ```

Yürütme planınızı kaydetmeniz gerekiyorsa, aşağıdaki komutu çalıştırın. Yer tutucuları ortamınız için uygun değerlerle değiştirin.

  ```bash
  terraform plan -out=<path>
  ```

Başka bir yararlı parametre [-var-dosyası](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Varsayılan olarak, Terlım, değişkenleri dosyanızı şu şekilde bulmaya çalıştı:
- `terraform.tfvars` adlı dosya
- İle adlı dosya şu kalıbı kullanıyor: `*.auto.tfvars`

Ancak, değişkenler dosyanız önceki iki kurallardan birini izlemelidir. Bu durumda, `-var-file` parametresiyle birlikte değişkenlerin dosya adını belirtin. Aşağıdaki örnekte bu nokta gösterilmektedir:

```hcl
terraform plan -var-file <my-variables-file.tf>
```

Terrayform, yapılandırma dosyasında belirtilen durumu elde etmek için gereken eylemleri belirler.

![Terraform yürütme planı oluşturma](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Terrayform yürütme planını uygulayın

Bu öğreticinin son adımı [terraform apply komutunu](https://www.terraform.io/docs/commands/apply.html) kullanarak `terraform plan` komutuyla oluşturulan eylem kümesini uygulamaktır.

En güncel yürütme planını uygulamak istiyorsanız şu komutu çalıştırın:

  ```bash
  terraform apply
  ```

Daha önce kaydedilmiş bir yürütme planı uygulamak istiyorsanız aşağıdaki komutu çalıştırın. Yer tutucuları ortamınız için uygun değerlerle değiştirin:

  ```bash
  terraform apply <path>
  ```

![Terraform yürütme planını uygulama](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Terrayform kullanarak bir Azure sanal makine ölçek kümesi oluşturma](terraform-create-vm-scaleset-network-disks-hcl.md)
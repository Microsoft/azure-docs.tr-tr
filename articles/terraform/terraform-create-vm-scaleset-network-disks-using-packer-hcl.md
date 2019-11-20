---
title: Öğretici-Terrayform kullanarak Packer özel görüntüsünden Azure sanal makine ölçek kümesi oluşturma
description: Terraform kullanarak Packer tarafından oluşturulan özel bir görüntüden (sanal ağ ve takılmış disklere sahip) Azure sanal makine ölçek kümesi yapılandırın ve sürüm oluşturun.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 9d149a28f82100715035f435de56ff134ca685f5
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159277"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Öğretici: Teroyform kullanarak Packer özel görüntüsünden Azure sanal makine ölçek kümesi oluşturma

Bu öğreticide [Terrayform](https://www.terraform.io/) kullanarak, [HashiCorp yapılandırma dilini](https://www.terraform.io/docs/configuration/syntax.html) (HCL) kullanan yönetilen disklerle birlikte [Packer](https://www.packer.io/intro/index.html) kullanılarak oluşturulan özel bir görüntüyle oluşturulmuş bir [Azure sanal makine ölçek kümesi](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) oluşturun ve dağıtın. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Terrayform dağıtımınızı ayarlayın.
> * Terrayform dağıtımı için değişkenleri ve çıkışları kullanın.
> * Ağ altyapısı oluşturun ve dağıtın.
> * Packer kullanarak özel bir sanal makine görüntüsü oluşturun.
> * Özel görüntüyü kullanarak bir sanal makine ölçek kümesi oluşturun ve dağıtın.
> * Bir JumpBox oluşturun ve dağıtın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- **Terrayform**: [terrayform 'U yükler ve Azure 'a erişimi yapılandırır](/azure/virtual-machines/linux/terraform-install-configure).
- **SSH anahtar çifti**: [SSH anahtar çifti oluşturun](/azure/virtual-machines/linux/mac-create-ssh-keys).
- **Packer**: [Packer 'ı yükler](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>Dosya yapısını oluşturun

Boş bir dizinde aşağıdaki adları kullanarak üç yeni dosya oluşturun:

- `variables.tf`: Bu dosya şablonda kullanılan değişkenlerin değerlerini içerir.
- `output.tf`: Bu dosya dağıtımdan sonra görüntülenecek ayarları açıklar.
- `vmss.tf`: Bu dosya, dağıtmakta olduğunuz altyapının kodunu içerir.

##  <a name="create-the-variables"></a>Değişkenleri oluşturma 

Bu adımda Terraform tarafından oluşturulan kaynakları özelleştiren değişkenleri tanımlayacaksınız.

`variables.tf` dosyasını düzenleyin, aşağıdaki kodu kopyalayın ve değişiklikleri kaydedin.

```hcl
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> Resource_group_name değişkeninin varsayılan değeri unset. Kendi değerini tanımlayın.

Dosyayı kaydedin.

Terkform şablonunuzu dağıttığınızda, uygulamaya erişmek için kullanılan tam etki alanı adını almak istersiniz. Terraform'un `output` kaynak türünü kullanın ve kaynağın `fqdn` özelliğini alın. 

`output.tf` dosyasını düzenleyin ve aşağıdaki kodu kopyalayarak tam etki alanı adını sanal makinelerin kullanımına açın. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Şablonda ağ altyapısını tanımlama 

Bu adımda yeni bir Azure kaynak grubunda aşağıdaki ağ altyapısını oluşturacaksınız: 
  - 10.0.0.0/16 adres alanına sahip bir sanal ağ.
  - Adres alanı 10.0.2.0/24 olan bir alt ağ.
  - İki genel IP adresi. Bunlardan biri, sanal makine ölçek kümesi yük dengeleyici tarafından kullanılır. Diğer bir deyişle SSH atlama kutusuna bağlanmak için kullanılır.

Ayrıca tüm kaynakların oluşturulacağı bir kaynak grubu da oluşturmanız gerekir. 

`vmss.tf` dosyasını düzenleyip aşağıdaki kodu kopyalayın: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = azurerm_resource_group.vmss.name
  virtual_network_name = azurerm_virtual_network.vmss.name
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Daha sonra tanımlanmasını kolaylaştırmak için Azure 'da dağıtılan kaynakları etiketleyerek.

## <a name="create-the-network-infrastructure"></a>Ağ altyapısını oluşturma

`.tf` dosyalarını oluşturduğunuz dizinde aşağıdaki komutu çalıştırarak Terraform ortamını başlatın:

```bash
terraform init 
```
 
Sağlayıcı eklentileri, Terrayform kayıt defterinden komutu çalıştırdığınız dizindeki `.terraform` klasörüne indirir.

Altyapıyı Azure'a dağıtmak için aşağıdaki komutu çalıştırın.

```bash
terraform apply
```

Genel IP adresinin tam etki alanı adının yapılandırmanıza gittiğinden emin olun.

![Genel IP adresi için sanal makine ölçek kümesi Terkform tam etki alanı adı](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Kaynak grubu aşağıdaki kaynakları kapsar:

![Sanal makine ölçek kümesi Terraform ağ kaynakları](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Packer kullanarak Azure görüntüsü oluşturma
[Azure 'Da Linux sanal makine görüntüleri oluşturmak Için Packer kullanma](/azure/virtual-machines/linux/build-image-with-packer)öğreticisindeki adımları izleyerek özel bir Linux görüntüsü oluşturun.
 
NGINX yüklü olarak önceden sağlanmış bir Ubuntu görüntüsü oluşturmak için öğreticiyi izleyin.

![Packer görüntüsünü oluşturduktan sonra bir görüntünüz vardır](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Bu öğreticinin amaçları doğrultusunda, Packer görüntüsünde, NGINX yüklemek için bir komut çalıştırılır. Oluşturma sırasında kendi betiğinizi de çalıştırabilirsiniz.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Altyapıyı düzenleyerek sanal makine ölçek kümesini ekleme

Bu adımda önceden dağıttığınız ağ üzerinde aşağıdaki kaynakları oluşturacaksınız:
- Uygulamayı sunacak bir Azure yük dengeleyici. Daha önce dağıtılan genel IP adresine iliştirin.
- Uygulamayı sunacak bir Azure yük dengeleyici ve kuralları. Daha önce yapılandırılmış genel IP adresine iliştirin.
- Bir Azure arka uç Adres Havuzu. Yük dengeleyiciye atayın.
- Uygulama tarafından kullanılan ve yük dengeleyicide yapılandırılan bir sistem durumu araştırması bağlantı noktası.
- Yük dengeleyicinin arkasında yer alan ve daha önce dağıtılan sanal ağ üzerinde çalışan bir sanal makine ölçek kümesi.
- Özel görüntüden yüklenen sanal makine ölçeğinin düğümlerinde [NGINX](https://nginx.org/) .


`vmss.tf` dosyasının sonuna aşağıdaki kodu ekleyin.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "ssh-running-probe"
  port                = var.application_port
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = azurerm_resource_group.vmss.name
  loadbalancer_id                = azurerm_lb.vmss.id
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = var.application_port
  backend_port                   = var.application_port
  backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = azurerm_lb_probe.vmss.id
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id=data.azurerm_image.image.id
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = azurerm_subnet.vmss.id
      load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
      primary = true
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Dağıtımı özelleştirmek için `variables.tf` dosyasına aşağıdaki kodu ekleyin:

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Sanal makine ölçek kümesini Azure'da dağıtma

Sanal makine ölçek kümesi dağıtımını görselleştirmek için aşağıdaki komutu çalıştırın:

```bash
terraform plan
```

Komutunu çıkışı şu görüntüye benzer olacaktır:

![Terraform sanal makine ölçek kümesi ekleme planı](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Ek kaynakları Azure'a dağıtın: 

```bash
terraform apply 
```

Kaynak grubunun içeriği şu görüntüye benzer olacaktır:

![Terraform sanal makine ölçek kümesi kaynak grubu](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Bir tarayıcı penceresi açın ve komutun döndürdüğü tam etki alanı adına bağlanın. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Var olan ağa sıçrama kutusu ekleme 

Bu isteğe bağlı adım, sıçrama kutusu kullanarak sanal makine ölçek kümesi örneklerine SSH erişimi sunar.

Var olan dağıtımınıza aşağıdaki kaynakları ekleyin:
- Sanal makine ölçek kümesi ile aynı alt ağa bağlı bir ağ arabirimi
- Bu ağ arabirimine sahip bir sanal makine

`vmss.tf` dosyasının sonuna aşağıdaki kodu ekleyin:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = azurerm_subnet.vmss.id
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = azurerm_public_ip.jumpbox.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = var.location
  resource_group_name   = azurerm_resource_group.vmss.name
  network_interface_ids = [azurerm_network_interface.jumpbox.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Dağıtım tamamlandığında sıçrama kutusu ana bilgisayar adını görüntüleyen aşağıdaki kodu eklemek için `outputs.tf` düzenleyin:

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>Sıçrama kutusu dağıtma

Sıçrama kutusu dağıtın.

```bash
terraform apply 
```

Dağıtım tamamlandıktan sonra, kaynak grubunun içeriği aşağıdaki görüntüye benzer şekilde görünür:

![Terraform sanal makine ölçek kümesi kaynak grubu](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Parola ile oturum açma, sıçrama kutusu ve dağıttığınız sanal makine ölçek kümesi üzerinde devre dışıdır. VM 'Lere erişmek için SSH ile oturum açın.

## <a name="clean-up-the-environment"></a>Ortamı temizleme

Aşağıdaki komutlar bu öğreticide oluşturulan kaynakları siler:

```bash
terraform destroy
```

Kaynakları silme işlemini onaylamanız istendiğinde *Evet* yazın. Yok etme işleminin tamamlanması birkaç dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure 'da Terrayform kullanma hakkında daha fazla bilgi edinin](/azure/terraform)

---
title: Öğretici-Azure Storage 'da Terrayform durumunu depolama
description: Azure depolama 'da Terrayform durumunu depolamanın bir girişi.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: d1b622a372be48bf044b512f3c964a5720fc3c5b
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159328"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Öğretici: Azure Storage 'da Terrayform durumunu depolama

Terrayform, dağıtılmış kaynakları Teraform yapılandırmalarına göre mutabık kılmak için kullanılır. Durum, Terüform 'un ekleme, güncelleştirme veya silme için Azure kaynaklarını bilmesini sağlar. Varsayılan olarak, `terraform apply` komutunu çalıştırdığınızda Terrayform durumu yerel olarak depolanır. Bu yapılandırma aşağıdaki nedenlerle ideal değildir:

- Yerel durum, bir takımda veya işbirliği ortamında iyi çalışmaz.
- Terrayform durumu hassas bilgiler içerebilir.
- Durumu yerel olarak depolama, yanlışlıkla silme olasılığını arttırır.

Terrayform, uzak depolamada durumu kalıcı hale getirmeyi destekler. Desteklenen bir arka uç, Azure Depolama ' dir. Bu belgede, Azure depolama 'nın bu amaçla nasıl yapılandırılacağı ve kullanılacağı gösterilmektedir.

## <a name="configure-storage-account"></a>Depolama hesabını yapılandırma

Azure Storage 'ı arka uç olarak kullanmadan önce bir depolama hesabı oluşturmanız gerekir. Depolama hesabı Azure portal, PowerShell, Azure CLı veya Terrayform ile oluşturulabilir. Azure CLı ile depolama hesabını yapılandırmak için aşağıdaki örneği kullanın.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Depolama hesabı adı, kapsayıcı adı ve depolama erişim anahtarı ' nı bir yere göz atın. Bu değerler, uzak durumu yapılandırırken gereklidir.

## <a name="configure-state-back-end"></a>Durum arka ucu yapılandırma

`terraform init` komutunu çalıştırdığınızda Terrayform durum arka ucu yapılandırılır. Durum arka ucu yapılandırmak için aşağıdaki veriler gereklidir:

- **storage_account_name**: Azure depolama hesabının adı.
- **container_name**: blob kapsayıcısının adı.
- **anahtar**: oluşturulacak durum depolama dosyasının adı.
- **access_key**: depolama erişim anahtarı.

Bu değerlerin her biri, Terrayform yapılandırma dosyasında veya komut satırında belirlenebilir. `access_key` değeri için bir ortam değişkeni kullanmanızı öneririz. Bir ortam değişkeni kullanmak, anahtarın diske yazılmasını engeller.

Azure depolama erişim anahtarı değeriyle `ARM_ACCESS_KEY` adlı bir ortam değişkeni oluşturun.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Azure depolama hesabı erişim anahtarını daha fazla korumak için Azure Key Vault ' de saklayın. Daha sonra, ortam değişkeni aşağıdakine benzer bir komut kullanılarak ayarlanabilir. Azure Key Vault hakkında daha fazla bilgi için [Azure Key Vault belgelerine](../key-vault/quick-create-cli.md)bakın.

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Terrayform 'u arka ucu kullanacak şekilde yapılandırmak için aşağıdaki adımların gerçekleştirilmesi gerekir:
- Bir tür `azurerm`olan `backend` bir yapılandırma bloğu ekleyin.
- Yapılandırma bloğuna bir `storage_account_name` değeri ekleyin.
- Yapılandırma bloğuna bir `container_name` değeri ekleyin.
- Yapılandırma bloğuna bir `key` değeri ekleyin.

Aşağıdaki örnek bir Terrayform arka ucu yapılandırır ve bir Azure Kaynak grubu oluşturur.

```hcl
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Aşağıdaki adımları uygulayarak yapılandırmayı başlatın:

1. `terraform init` komutunu çalıştırın.
1. `terraform apply` komutunu çalıştırın.

Şimdi de durum dosyasını Azure Storage blob 'unda bulabilirsiniz.

## <a name="state-locking"></a>Durum kilitleme

Azure depolama Blobları, durum yazan herhangi bir işlemden önce otomatik olarak kilitlenir. Bu model, eş zamanlı durum işlemlerini önler ve bu da bozulmaya neden olabilir. 

Daha fazla bilgi için, bkz. Terrayform belgelerindeki [durum kilitleme](https://www.terraform.io/docs/state/locking.html) .

Azure portal veya diğer Azure yönetim araçları aracılığıyla blobu incelediğinizde kilidi görebilirsiniz.

![Kilitleme ile Azure blobu](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme

Bir Azure blobuna depolanan veriler kalıcı olmadan önce şifrelenir. Gerekli olduğunda Terrayform, durumu arka uçta alır ve yerel bellekte depolar. Bu düzenin kullanıldığı durum, yerel diskinize hiçbir şekilde yazılmaz.

Azure depolama şifrelemesi hakkında daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama hizmeti şifrelemesi](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure 'da Terrayform kullanma hakkında daha fazla bilgi edinin](/azure/terraform)
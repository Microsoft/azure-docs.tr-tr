---
title: Azure kaynaklarını sağlamak için Terrayform 'u yükleyip yapılandırın
description: Azure kaynakları oluşturmak için Terrayform 'u yüklemeyi ve yapılandırmayı öğrenin
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
ms.openlocfilehash: efba440448ac912b7656eeab017eef947ab25e95
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034697"
---
# <a name="install-and-configure-terraform-to-provision-azure-resources"></a>Azure kaynaklarını sağlamak için Terrayform 'u yükleyip yapılandırın
 
Terrayform [basit bir şablon oluşturma dili](https://www.terraform.io/docs/configuration/syntax.html)kullanarak bulut altyapısını tanımlamaya, önizlemeye ve dağıtmaya yönelik kolay bir yol sağlar. Bu makalede, Azure 'da kaynak sağlamak için Terrayform kullanmak için gereken adımlar açıklanmaktadır.

Azure ile Terrayform kullanma hakkında daha fazla bilgi edinmek için [Terrayform hub 'ını](/azure/terraform)ziyaret edin.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terrayform, [Cloud Shell](/azure/terraform/terraform-cloud-shell)varsayılan olarak yüklenir. Terrayform 'u yerel olarak yüklemeyi tercih ederseniz bir sonraki adımı tamamlayarak [Azure 'A teraform erişimini ayarlamaya](#set-up-terraform-access-to-azure)devam edin.

## <a name="install-terraform"></a>Terrayform 'u yükler

Terrayform 'u yüklemek için, işletim sisteminiz için uygun paketi ayrı bir yükleme dizinine [indirin](https://www.terraform.io/downloads.html) . İndirme, bir genel yol tanımlamanız gereken tek bir yürütülebilir dosya içerir. Linux ve Mac 'te yolun nasıl ayarlanacağı hakkında yönergeler için [Bu Web sayfasına](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)gidin. Windows 'da yolun nasıl ayarlanacağı hakkında yönergeler için [Bu Web sayfasına](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)gidin.

`terraform` komutuyla yol yapılandırmanızı doğrulayın. Aşağıdaki örnek çıktıda olduğu gibi, kullanılabilir Terrayform seçeneklerinin bir listesi gösterilir:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Azure 'a Terrayform erişimi kurma

Terlarform 'un Azure 'a kaynak sağlamasını sağlamak için bir [Azure AD hizmet sorumlusu](/cli/azure/create-an-azure-service-principal-azure-cli)oluşturun. Hizmet sorumlusu, Azure aboneliğinizdeki kaynakları sağlamak için Teraform betiklerinizi verir.

Birden çok Azure aboneliğiniz varsa, ilk olarak hesabınızı [az Account List](/cli/azure/account#az-account-list) ile sorgulayın ve abonelik kimliği ve Kiracı kimliği değerlerinin bir listesini alın:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Seçili bir aboneliği kullanmak için, bu oturum için aboneliği [az Account set](/cli/azure/account#az-account-set)ile ayarlayın. `SUBSCRIPTION_ID` ortam değişkenini, kullanmak istediğiniz aboneliğin döndürülen `id` alanının değerini tutacak şekilde ayarlayın:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Artık Terrayform ile kullanmak üzere bir hizmet sorumlusu oluşturabilirsiniz. [Az ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)' i kullanın ve *kapsamı* aboneliğinize aşağıdaki şekilde ayarlayın:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

`appId`, `password`, `sp_name`ve `tenant` döndürülür. `appId` ve `password`bir kopyasını alın.

## <a name="configure-terraform-environment-variables"></a>Terrayform ortam değişkenlerini yapılandırma

Terrayform 'u Azure AD hizmet sorumlunuzu kullanacak şekilde yapılandırmak için, daha sonra [Azure teraform modülleri](https://registry.terraform.io/modules/Azure)tarafından kullanılan aşağıdaki ortam değişkenlerini ayarlayın. Ayrıca, Azure genel dışında bir Azure bulutu ile çalışıyorsa ortamı da ayarlayabilirsiniz.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Bu değişkenleri ayarlamak için aşağıdaki örnek kabuk betiğini kullanabilirsiniz:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Örnek komut dosyası çalıştırma

Boş bir dizinde `test.tf` bir dosya oluşturun ve aşağıdaki komut dosyasına yapıştırın.

```hcl
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Dosyayı kaydedin ve ardından Terrayform dağıtımını başlatın. Bu adım, Azure Kaynak grubu oluşturmak için gereken Azure modüllerini indirir.

```bash
terraform init
```

Çıktı aşağıdaki örneğe benzer:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

`terraform plan`olan Terrayform betiği tarafından tamamlanacak eylemlerin önizlemesini yapabilirsiniz. Kaynak grubunu oluşturmaya hazırsanız, Terrayform planınızı aşağıdaki gibi uygulayın:

```bash
terraform apply
```

Çıktı aşağıdaki örneğe benzer:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure kimlik bilgilerini yapılandırmak ve Azure aboneliğinizde kaynak oluşturmaya başlamak için Terrayform 'u yüklediniz veya Cloud Shell kullandınız. Azure 'da daha kapsamlı bir Teraform dağıtımı oluşturmak için aşağıdaki makaleye bakın:

> [!div class="nextstepaction"]
> [Terrayform ile Azure VM oluşturma](terraform-create-complete-vm.md)

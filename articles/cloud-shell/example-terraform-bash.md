---
title: Azure Cloud Shell | öğesinden Terrayform ile dağıtma | Microsoft Docs
description: Azure Cloud Shell 'tan Terrayform ile dağıtma
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: f5939251729905d349b79a94411cf87e3873b279
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742075"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Azure Cloud Shell Bash 'ten Terrayform ile dağıtma
Bu makalede, [Terrayform Azurerd sağlayıcısına](https://www.terraform.io/docs/providers/azurerm/index.html)sahip bir kaynak grubu oluşturma işlemi adım adım açıklanmaktadır. 

[HashiCorp Terrayform](https://www.terraform.io/) , API 'leri, düzenlenecek, gözden geçirilmesi ve sürümü oluşturulacak ekip üyeleri arasında paylaşılabilen, bildirime dayalı yapılandırma dosyalarına ortak bir şekilde tanımlayan açık kaynaklı bir araçtır. Microsoft Azurere sağlayıcısı, Azure Resource Manager tarafından desteklenen kaynaklarla Azurerd API 'Leri aracılığıyla etkileşim kurmak için kullanılır. 

## <a name="automatic-authentication"></a>Otomatik kimlik doğrulama
Terrayform, varsayılan olarak Cloud Shell Bash 'e yüklenir. Ayrıca, Cloud Shell Azure modülleri aracılığıyla kaynak dağıtmak için varsayılan Azure CLı aboneliğinizin kimliğini otomatik olarak doğrular.

Terrayform, ayarlanan varsayılan Azure CLı aboneliğini kullanır. Varsayılan abonelikleri güncelleştirmek için şunu çalıştırın:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Kılavuz
### <a name="launch-bash-in-cloud-shell"></a>Cloud Shell 'da Bash 'i başlatın
1. Tercih ettiğiniz konumdan Cloud Shell başlatın
2. Tercih ettiğiniz aboneliğin ayarlandığını doğrulayın

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Terrayform şablonu oluşturma
Tercih ettiğiniz metin düzenleyicinizle main.tf adlı yeni bir Terrayform şablonu oluşturun.

```
vim main.tf
```

Aşağıdaki kodu kopyalayıp Cloud Shell yapıştırın.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Dosyanızı kaydedin ve metin düzenleyicinizde çıkın.

### <a name="terraform-init"></a>Terrayform init
' İ çalıştırarak `terraform init`başlayın.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Terrayform [başlatma komutu](https://www.terraform.io/docs/commands/init.html) , teraform yapılandırma dosyalarını içeren bir çalışma dizinini başlatmak için kullanılır. `terraform init` Komut, yeni bir teraform yapılandırması yazdıktan veya sürüm denetiminden var olan bir yapılandırma oluşturulduktan sonra çalıştırılması gereken ilk komuttur. Bu komutu birden çok kez çalıştırmak güvenlidir.

### <a name="terraform-plan"></a>Terraform plan
Terrayform şablonu tarafından oluşturulacak kaynakları ile `terraform plan`önizleyin.

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

[terraform plan komutu](https://www.terraform.io/docs/commands/plan.html), yürütme planı oluşturmak için kullanılır. Terrayform, açıkça devre dışı bırakılmadığı takdirde yenileme gerçekleştirir ve ardından yapılandırma dosyalarında belirtilen istenen duruma ulaşmak için hangi eylemlerin gerekli olduğunu belirler. Plan,-Out kullanılarak kaydedilebilir ve sonra yalnızca ön planlı eylemlerin yürütülmesini sağlamak için terrayform 'a sağlanır.

### <a name="terraform-apply"></a>Terraform apply
İle `terraform apply`Azure kaynaklarını sağlayın.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Terrayform Apply komutu](https://www.terraform.io/docs/commands/apply.html) , yapılandırmanın istenen durumuna ulaşmak için gereken değişiklikleri uygulamak için kullanılır.

### <a name="verify-deployment-with-azure-cli"></a>Azure CLı ile dağıtımı doğrulama
Kaynağın `az group show -n myRgName` başarıyla sağlanması gerektiğini doğrulamak için öğesini çalıştırın.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Terrayform yok etme ile temizle
Terrayform tarafından oluşturulan altyapıyı temizlemek için [terkform Destroy komutuyla](https://www.terraform.io/docs/commands/destroy.html) oluşturulan kaynak grubunu temizleyin.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Terrayform aracılığıyla bir Azure kaynağını başarıyla oluşturdunuz. Cloud Shell hakkında öğrenmeye devam etmek için sonraki adımları ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar
[Teraform Azure sağlayıcısı hakkında bilgi edinin](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash Cloud Shell hızlı başlangıç](quickstart.md)

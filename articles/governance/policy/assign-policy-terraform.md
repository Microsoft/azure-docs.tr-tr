---
title: 'Hızlı başlangıç: Terrayform ile yeni ilke ataması'
description: Bu hızlı başlangıçta, uyumlu olmayan kaynakları belirlemek üzere bir ilke ataması oluşturmak için Terrayform ve HCL sözdizimini kullanırsınız.
ms.date: 03/31/2021
ms.topic: quickstart
ms.openlocfilehash: 6c1a43010c48f71872d935a8fb52f608b479da8a
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092356"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Hızlı başlangıç: Terrayform kullanarak uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir.
Bu hızlı başlangıç, yönetilen disk kullanmayan sanal makineleri belirlemek üzere ilke ataması oluşturma işleminde size yol gösterir.

Bu işlemin sonunda, yönetilen disk kullanmayan sanal makineleri başarılı bir şekilde belirlemiş olacaksınız. Bu sanal makineler, ilke ataması ile _uyumsuzdur_.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.
- Ortamınızda yapılandırılmış [Terrayform](https://www.terraform.io/) sürüm 0.12.0 veya üstü.
  Yönergeler için bkz. [Azure Cloud Shell kullanarak Terrayform yapılandırma](/azure/developer/terraform/get-started-cloud-shell).
- Bu hızlı başlangıç, Azure CLı sürüm 2.13.0 veya üstünü çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Terrayform yapılandırması, değişkeni ve çıkış dosyası oluşturma

Bu hızlı başlangıçta, bir ilke ataması oluşturup yönetilen diskler () tanımını **kullanmayan denetim sanal** makinelerini atarsınız `06a78e20-9358-41c9-923c-fb736d382a4d` . Bu ilke tanımı, ilke tanımında ayarlanan koşullarla uyumlu olmayan kaynakları belirler.

İlk olarak, Terrayform yapılandırmasını, değişkenini ve çıkış dosyalarını yapılandırın. Azure Ilkesi için Terrayform kaynakları [Azure sağlayıcısını](https://www.terraform.io/docs/providers/azurerm/index.html)kullanır.

1. Adlı yeni bir klasör oluşturun `policy-assignment` ve dizinleri bu klasöre değiştirin.

1. `main.tf`Aşağıdaki kodla oluşturun:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. `variables.tf`Aşağıdaki kodla oluşturun:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   Kapsam, ilke atamasının hangi kaynaklarda veya kaynak gruplarında uygulanacağını belirler. Bir yönetim grubundan tek bir kaynağa kadar değişebilir. `{scope}`Aşağıdaki desenlerden biriyle değiştirdiğinizden emin olun:

   - Abonelik: `/subscriptions/{subscriptionId}`
   - Kaynak grubu: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Kaynak `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. `output.tf`Aşağıdaki kodla oluşturun:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Terrayform 'u başlatma ve plan oluşturma

Ardından, gerekli sağlayıcıları indirmek için Terrayform 'u başlatın ve ardından bir plan oluşturun.

1. [Terrayform init](https://www.terraform.io/docs/commands/init.html) komutunu çalıştırın. Bu komut, Azure kaynaklarını Terrayform yapılandırmasında oluşturmak için gereken Azure modüllerini indirir.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Azurerd modülünü ve başarı iletisini indirmeyi gösteren terazform init komutunu çalıştırmanın ekran görüntüsü.":::

1. Terrayform için [Azure CLI](/cli/azure/) ile kimlik doğrulama. Daha fazla bilgi için bkz. Azure [sağlayıcı: Azure CLI kullanarak kimlik doğrulaması](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. [Terrayform plan](https://www.terraform.io/docs/commands/plan.html) komutu ve **Out** parametresiyle yürütme planı oluşturun.

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Oluşturulan Azure kaynağını göstermek için terırform plan komutu ve out parametresini çalıştırmanın ekran görüntüsü.":::

   > [!NOTE]
   > Kalıcı yürütme planları ve güvenlik hakkında daha fazla bilgi için bkz. [Terrayform planı: güvenlik uyarısı](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>Terraform yürütme planını uygulama

Son olarak, yürütme planını uygulayın.

[Teraform Apply](https://www.terraform.io/docs/commands/apply.html) komutunu çalıştırın ve önceden oluşturulmuş ' i belirtin `assignment.tfplan` .

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Terlarform Apply komutunu ve elde edilen kaynak oluşturmayı çalıştırmanın ekran görüntüsü.":::

"Uygula Tamam! Kaynaklar: 1 eklendi, 0 değiştirildi, 0 yok edildi. " ileti, ilke ataması artık oluşturulmuştur. Dosyayı tanımladığımızdan `outputs.tf` , _atama \_ kimliği_ de döndürülür.

## <a name="identify-non-compliant-resources"></a>Uyumlu olmayan kaynakları belirleme

Bu yeni atama altında uyumlu olmayan kaynakları görüntülemek için tarafından döndürülen _atama \_ kimliğini_ kullanın `terraform apply` . İle, bir JSON dosyasına çıktı olan uyumlu olmayan kaynakların kaynak kimliklerini almak için aşağıdaki komutu çalıştırın:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Sonuçlarınız aşağıdaki örneğe benzer:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Sonuçlar, Azure portalı görünümünde **Uyumlu olmayan kaynaklar** bölümünde gördüklerinize benzer.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturulan atamayı kaldırmak için Azure CLı kullanın veya Teraform yürütme planını ile ters çevirin `terraform destroy` .

- Azure CLI’si

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure ortamınızda uyumlu olmayan kaynakları belirlemek üzere bir ilke tanımı atadınız.

Yeni kaynakların uyumlu olduğunu doğrulamak üzere ilke atama hakkında daha fazla bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [İlke oluşturma ve yönetme](./tutorials/create-and-manage.md)

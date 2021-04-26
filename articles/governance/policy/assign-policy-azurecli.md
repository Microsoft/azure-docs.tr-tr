---
title: 'Hızlı başlangıç: Azure CLı ile yeni ilke ataması'
description: Bu hızlı başlangıçta, uyumlu olmayan kaynakları belirlemek üzere bir Azure Ilkesi ataması oluşturmak için Azure CLı 'yi kullanırsınız.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2b7dad3eba18966a78fd35211ce40ea48234da30
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090194"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Hızlı başlangıç: Azure CLı ile uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir.
Bu hızlı başlangıç, yönetilen disk kullanmayan sanal makineleri belirlemek üzere ilke ataması oluşturma işleminde size yol gösterir.

Bu işlemin sonunda, yönetilen disk kullanmayan sanal makineleri başarılı bir şekilde belirlemiş olacaksınız. Bu sanal makineler, ilke ataması ile _uyumsuzdur_.

Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını yönetmek veya bu kaynakları oluşturmak için kullanılır. Bu kılavuzda, Azure ortamınızda uyumlu olmayan kaynakları belirlemek ve bir ilke ataması oluşturmak için Azure CLI kullanılır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- Bu hızlı başlangıç, Azure CLı sürüm 2.0.76 veya üstünü çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

- Azure CLı kullanarak Azure Policy Insights kaynak sağlayıcısını kaydedin. Kaynak sağlayıcısı kaydedildiğinde, aboneliğinizin bununla çalıştığından emin olunur. Kaynak sağlayıcısını kaydetmek için, kaynak sağlayıcısı kaydetme işlemini kaydettirme izninizin olması gerekir. Bu işlem, Katkıda Bulunan ve Sahip rolleriyle birlikte sunulur. Aşağıdaki komutu çalıştırarak kaynak sağlayıcısını kaydedin:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Kaynak sağlayıcıları kaydetme ve görüntülemeyle ilgili daha fazla bilgi için bkz. [Kaynak Sağlayıcıları ve Türleri](../../azure-resource-manager/management/resource-providers-and-types.md)

- Henüz yapmadıysanız [ARMClient](https://github.com/projectkudu/ARMClient)’ı yükleyin. Bu, HTTP isteklerini Azure Resource Manager tabanlı API’lere gönderen bir araçtır.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>İlke ataması oluşturma

Bu hızlı başlangıçta, bir ilke ataması oluşturup **Yönetilen diskleri kullanmayan sanal makineleri denetle** tanımını atayacaksınız. Bu ilke tanımı, ilke tanımında ayarlanan koşullarla uyumlu olmayan kaynakları belirler.

İlke ataması oluşturmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

Yukarıdaki komutta aşağıdaki bilgiler kullanılmaktadır:

- **Ad** - Atamanın gerçek adı. Bu örnekte _audit-vm-manageddisks_ kullanıldı.
- **Görünen Ad** - Bu ilke atamasının görünen adı. Bu durumda, _yönetilen disk ataması olmadan VM 'Leri denetleme_ kullanıyorsunuz demektir.
- **İlke** - Bu, atamayı oluşturmak için kullandığınız ilke tanımı kimliğidir. Bu durumda, _yönetilen diskleri kullanmayan ilke tanımı denetim VM_'lerinin kimliğidir. İlke tanımı kimliğini almak için şu komutu çalıştırın: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Kapsam** - Kapsam, ilke atamasının hangi kaynaklarda veya kaynak gruplarında uygulanacağını belirler. Bir abonelikten kaynak gruplarına kadar değişiklik gösterebilir. &lt;Kapsam&gt; yerine kaynak grubunuzun adını yazdığınızdan emin olun.

## <a name="identify-non-compliant-resources"></a>Uyumlu olmayan kaynakları belirleme

Bu yeni atama altında uyumlu olmayan kaynakları görüntülemek için aşağıdaki komutları çalıştırarak ilke ataması kimliğini alın:

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

İlke atama kimlikleri hakkında daha fazla bilgi için, bkz. [az Policy atama](/cli/azure/policy/assignment).

Daha sonra, JSON dosyasına çıkarılan uyumlu olmayan kaynakların kaynak kimliklerini almak için aşağıdaki komutu çalıştırın:

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

Oluşturulan atamayı kaldırmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure ortamınızda uyumlu olmayan kaynakları belirlemek üzere bir ilke tanımı atadınız.

Yeni kaynakların uyumlu olduğunu doğrulamak üzere ilke atama hakkında daha fazla bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [İlke oluşturma ve yönetme](./tutorials/create-and-manage.md)

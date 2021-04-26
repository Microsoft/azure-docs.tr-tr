---
title: Azure DevTest Labs 'de VM 'Ler için kaynak grubu belirtin | Microsoft Docs
description: Azure DevTest Labs bir laboratuvardaki VM 'Ler için kaynak grubu belirtmeyi öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c6f576a20fc8fada9dd515e8ba2a266761a3e586
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377497"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Laboratuvar sanal makineleri için Azure DevTest Labs bir kaynak grubu belirtin

Laboratuvar sahibi olarak, laboratuar sanal makinelerinizi belirli bir kaynak grubunda oluşturulacak şekilde yapılandırabilirsiniz. Bu özellik aşağıdaki senaryolarda size yardımcı olur:

- Aboneliğinizde Labs tarafından oluşturulan daha az kaynak grubu vardır.
- Laboratuvarlarınızın yapılandırdığınız bir kaynak grupları kümesi içinde çalışmasını sağlayabilirsiniz.
- Azure aboneliğinizde kaynak grupları oluşturmak için gereken kısıtlamaların ve onayların etrafında çalışın.
- Bu kaynakları izlemeyi ve kaynak grubu düzeyinde kaynakları yönetmek üzere [ilke](../governance/policy/overview.md) uygulamayı kolaylaştırmak için tüm laboratuar kaynaklarınızı tek bir kaynak grubu içinde birleştirin.

Bu özellik ile, tüm Laboratuvar sanal makinelerinizdeki Azure aboneliğinizde yeni veya mevcut bir kaynak grubu belirtmek için bir komut dosyası kullanabilirsiniz. Şu anda bu özelliği bir API aracılığıyla destekler Azure DevTest Labs.

> [!NOTE]
> DevTest Labs 'de Labs oluşturduğunuzda tüm abonelik limitleri geçerlidir. Laboratuvarınızı aboneliğinizdeki herhangi bir kaynak olarak düşünün. Kaynak grupları söz konusu olduğunda sınır, [abonelik başına 980 kaynak gruplarıdır](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Azure portalı kullanma
Laboratuvarda oluşturulan tüm VM 'Ler için bir kaynak grubu belirtmek üzere bu adımları izleyin. 

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol gezinti menüsünde **tüm hizmetler** ' i seçin. 
3. Listeden **DevTest Labs**'i seçin.
4. Laboratuvarlar listesinden **laboratuvarınızı** seçin.  
5. Sol menüdeki **Ayarlar** bölümünde **yapılandırma ve ilkeler** ' i seçin. 
6. Soldaki menüden **Laboratuvar ayarları** ' nı seçin. 
7. **Bir kaynak grubundaki tüm sanal makineleri** seçin. 
8. Açılan listede var olan bir kaynak grubunu seçin (veya) **Yeni oluştur**' u seçin, kaynak grubu için bir **ad** girin ve **Tamam**' ı seçin. 

    ![Tüm laboratuvar VM 'Leri için kaynak grubunu seçin](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>PowerShell kullanma 
Aşağıdaki örnekte, yeni bir kaynak grubundaki tüm Laboratuvar sanal makinelerini oluşturmak için bir PowerShell betiğinin nasıl kullanılacağı gösterilmektedir.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Aşağıdaki komutu kullanarak betiği çağırın. ResourceGroup.ps1, önceki betiği içeren dosyadır:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma
Laboratuvar oluşturmak için bir Azure Resource Manager şablonu kullanıyorsanız, aşağıdaki örnekte gösterildiği gibi, şablonunuzun laboratuvar özellikleri bölümünde bulunan **Vmcreationresourcegroupıd** özelliğini kullanın:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018-10-15-preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>Laboratuvar VM 'Leri için bir kaynak grubunu yapılandırmak için API
Bu API 'YI kullanırken laboratuvar sahibi olarak aşağıdaki seçeneklere sahipsiniz:

- Tüm sanal makineler için **laboratuvarın kaynak grubunu** seçin.
- Tüm sanal makineler için laboratuvarın kaynak grubu dışında **mevcut bir kaynak grubu** seçin.
- Tüm sanal makineler için **Yeni bir kaynak grubu** adı girin.
- Laboratuvardaki her VM için bir kaynak grubunun oluşturulduğu mevcut davranışı kullanmaya devam edin.
 
Bu ayar, laboratuvarda oluşturulan yeni sanal makineler için geçerlidir. Laboratuvarınızda kendi kaynak gruplarında oluşturulan eski VM 'Ler etkilenmemiştir. Laboratuvarınızda oluşturulan ortamlar kendi kaynak gruplarında kalmaya devam eder.

Bu API 'yi kullanma:
- API sürüm **2018-10-15-önizleme** kullanın.
- Yeni bir kaynak grubu belirtirseniz, aboneliğinizdeki **kaynak gruplarında yazma izinlerine** sahip olduğunuzdan emin olun. Yazma izinlerinizin olmaması durumunda, belirtilen kaynak grubunda yeni sanal makineler oluşturmak başarısız olur.
- API kullanırken, **tam kaynak grubu kimliğini** geçirin. Örneğin: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Kaynak grubunun laboratuvara aynı abonelikte olduğundan emin olun. 


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın: 

- [Laboratuvar ilkelerini ayarlama](devtest-lab-set-lab-policy.md)
- [Sık sorulan sorular](devtest-lab-faq.md)

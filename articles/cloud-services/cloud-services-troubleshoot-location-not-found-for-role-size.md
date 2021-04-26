---
title: Azure 'a bir bulut hizmeti (klasik) dağıttığınızda LocationNotFoundForRoleSize sorunlarını giderme | Microsoft Docs
description: Bu makalede, Azure 'a bir bulut hizmeti (klasik) dağıtımında bir LocationNotFoundForRoleSize özel durumunun nasıl çözümleneceği gösterilmektedir.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 54af2387ec0ff6c8f86f96821baad17736e8d85b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877975"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Azure 'a bir bulut hizmeti (klasik) dağıttığınızda LocationNotFoundForRoleSize sorunlarını giderme

Bu makalede, bir Azure bulut hizmeti dağıtırken (klasik) bir sanal makine (VM) boyutu kullanılabilir olmadığında oluşan ayırma hatalarıyla ilgili sorunları giderebilirsiniz.

Bir bulut hizmetine (klasik) örnekler dağıtırken veya yeni Web veya çalışan rolü örnekleri eklediğinizde Microsoft Azure işlem kaynaklarını ayırır.

Azure abonelik sınırına ulaşmadan önce bile bu işlemler sırasında bazen hatalar alabilirsiniz.

> [!TIP]
> Ayrıca, hizmetlerinizin dağıtımını planlarken bilgiler de yararlı olabilir.

## <a name="symptom"></a>Belirti

Azure portal, bulut hizmetinize (klasik) gidin ve kenar çubuğu 'nda günlükleri görüntülemek için *işlem günlüğü (klasik)* seçeneğini belirleyin.

![Görüntü, Işlem günlüğü (klasik) dikey penceresini gösterir.](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Bulut hizmetinizin (klasik) günlüklerini incelerken aşağıdaki özel durumu görürsünüz:

|Özel durum türü  |Hata İletisi  |
|---------|---------|
|LocationNotFoundForRoleSize     |' ' İşlemi `{Operation ID}` başarısız oldu: ' Istenen VM katmanı Şu anda `{Region ID}` Bu abonelik için bölgede () kullanılamıyor. Lütfen başka bir katman deneyin veya farklı bir konuma dağıtın. '.|

## <a name="cause"></a>Nedeni

Dağıttığınız bölge veya kümeyle ilgili bir kapasite sorunu vardır. Seçtiğiniz kaynak SKU 'SU (VM boyutu) belirtilen bölge için kullanılabilir olmadığında *Locationnotfoundforrolesize* özel durumu oluşur.

## <a name="solution"></a>Çözüm

Bu senaryoda, bulut hizmetinizi (klasik) dağıtmak için farklı bir bölge veya SKU seçmeniz gerekir. Bulut hizmetinizi (klasik) dağıtmak veya yükseltmeden önce, bir bölgede veya kullanılabilirlik alanında hangi SKU 'Ların kullanılabildiğini belirleyebilirsiniz. Aşağıdaki [Azure CLI](#list-skus-in-region-using-azure-cli), [PowerShell](#list-skus-in-region-using-powershell)veya [REST API](#list-skus-in-region-using-rest-api) süreçlerini izleyin.

### <a name="list-skus-in-region-using-azure-cli"></a>Azure CLı kullanarak bölgedeki SKU 'Ları listeleme

[Az VM List-SKU] (/cli/Azure/VM? View = Azure-CLI-latest) kullanabilirsiniz.
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) komutu.

- Kullanmakta olduğunuz `--location` konuma çıktıyı filtrelemek için parametresini kullanın.
- `--size`Kısmi bir boyut adına göre arama yapmak için parametresini kullanın.
- Daha fazla bilgi için bkz. [SKU kullanılamıyor Kılavuzu Için çözüm hatası](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Örneğin:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Örnek sonuçlar:** ![ ' Az VM List-SKU--location Güneydoğu ABD--boyut Standard_F--çıkış tablosu ' komutunun çalıştırıldığı Azure CLı çıkışı kullanılabilir SKU 'Ları gösterir.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>PowerShell kullanarak bölgedeki SKU 'Ları listeleme

[Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) komutunu kullanabilirsiniz.

- Sonuçları konuma göre filtreleyin.
- Bu komut için en son PowerShell sürümüne sahip olmanız gerekir.
- Daha fazla bilgi için bkz. [SKU kullanılamıyor Kılavuzu Için çözüm hatası](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) .

**Örneğin:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Diğer bazı yararlı komutlar:**

Boyut (Standard_DS14_v2) içeren konumları filtreleyin:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Boyut (v3) içeren tüm konumları filtreleyin:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>REST API kullanarak bölgede SKU 'Ları listeleme

[Kaynak SKU 'ları-listeleme](/rest/api/compute/resourceskus/list) işlemini kullanabilirsiniz. Kullanılabilir SKU 'Ları ve bölgeleri aşağıdaki biçimde döndürür:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla ayırma hatası çözümü ve bunların nasıl oluşturulduğunu daha iyi anlamak için:

> [!div class="nextstepaction"]
> [Ayırma arızaları-bulut hizmeti (klasik)](cloud-services-allocation-failures.md)

Azure sorununuz bu makalede giderilmemişse [MSDN ve Stack Overflow](https://azure.microsoft.com/support/forums/)Azure forumlarını ziyaret edin. Sorununuzu bu forumlara gönderebilir veya [ @AzureSupport Twitter 'da](https://twitter.com/AzureSupport)ilan edebilirsiniz. Ayrıca, bir Azure destek isteği gönderebilirsiniz. Destek isteği göndermek için [Azure desteği](https://azure.microsoft.com/support/options/) sayfasında *Destek Al*' ı seçin.

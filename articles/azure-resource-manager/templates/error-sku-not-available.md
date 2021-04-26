---
title: SKU kullanılamıyor hatası
description: Azure Resource Manager ile kaynak dağıtımında SKU kullanılamıyor hatası ile ilgili sorunların nasıl giderileceği açıklanmaktadır.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.openlocfilehash: 3baedf6a5c9f2dbfd3ddf666b458fac649fce2ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503907"
---
# <a name="resolve-errors-for-sku-not-available"></a>Kullanılamayan SKU’larla ilgili hataları giderme

Bu makalede, **Skunotavailable** hatasının nasıl çözümleneceği açıklanır. Bu bölgede/bölgede veya iş gereksinimlerinizi karşılayan alternatif bir bölgede/bölgede uygun bir SKU bulamıyorsanız Azure desteği 'ne bir [SKU isteği](/troubleshoot/azure/general/region-access-request-process) gönderebilirsiniz.

## <a name="symptom"></a>Belirti

Bir kaynak (genellikle bir sanal makine) dağıttığınızda aşağıdaki hata kodunu ve hata iletisini alırsınız:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Nedeni

Seçtiğiniz kaynak SKU 'SU (VM boyutu gibi) seçtiğiniz konum için kullanılabilir olmadığında bu hatayı alırsınız.

Azure spot VM veya spot ölçek kümesi örneği dağıtıyorsanız, bu konumda Azure noktası için herhangi bir kapasite yoktur. Daha fazla bilgi için bkz. [spot hata iletileri](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Çözüm 1-PowerShell

Bir bölgede/bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) komutunu kullanın. Sonuçları konuma göre filtreleyin. Bu komut için en son PowerShell sürümüne sahip olmanız gerekir.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Sonuçlar, konum ve bu SKU için herhangi bir kısıtlama için SKU 'ların bir listesini içerir. SKU 'nun olarak listelendiğine dikkat edin `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Konuma ve SKU 'ya göre filtrelemek için şunu kullanın:

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

Komut şunun gibi sonuçlar döndürür:

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>Çözüm 2-Azure CLı

Bir bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için [az VM List-SKU](/cli/azure/vm#az_vm_list_skus) komutunu kullanın. `--location`Çıktıyı konuma göre filtrelemek için parametresini kullanın. `--size`Kısmi bir boyut adına göre arama yapmak için parametresini kullanın. `--all`Geçerli abonelik için kullanılamayan boyutlar dahil olmak üzere tüm bilgileri göstermek için parametresini kullanın.

Azure CLı sürüm 2.15.0 veya sonraki bir sürümü olmalıdır. Sürümünüzü denetlemek için kullanın `az --version` . Gerekirse, [yüklemenizi güncelleştirin](/cli/azure/update-azure-cli).

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

Komut şunun gibi sonuçlar döndürür:

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>Çözüm 3-Azure portal

Bir bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için [portalını](https://portal.azure.com)kullanın. Portalda oturum açın ve arabirim aracılığıyla bir kaynak ekleyin. Değerleri ayarladığınız sürece bu kaynak için kullanılabilir SKU 'Ları görürsünüz. Dağıtımı doldurmanız gerekmez.

Örneğin, bir sanal makine oluşturma işlemini başlatın. Kullanılabilir diğer boyutu görmek için **boyutu Değiştir**' i seçin.

![VM oluşturma](./media/error-sku-not-available/create-vm.png)

Kullanılabilir boyutlarda filtre uygulayabilir ve bu boyutları kaydırabilirsiniz.

![Kullanılabilir SKU'lar](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Çözüm 4-REST

Bir bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için [kaynak SKU 'lar-listeleme](/rest/api/compute/resourceskus/list) işlemini kullanın.

Kullanılabilir SKU 'Ları ve bölgeleri aşağıdaki biçimde döndürür:

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
    ...
  ]
}
```
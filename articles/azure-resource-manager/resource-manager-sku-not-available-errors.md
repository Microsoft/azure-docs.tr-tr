---
title: SKU kullanılamıyor hatası
description: Azure Resource Manager ile kaynak dağıtımında SKU kullanılamıyor hatası ile ilgili sorunların nasıl giderileceği açıklanmaktadır.
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.openlocfilehash: 56afca6b6a59ca08f3fd59c4d9b3ebf12bda415a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150485"
---
# <a name="resolve-errors-for-sku-not-available"></a>SKU kullanılamıyor için hataları çözün

Bu makalede, **Skunotavailable** hatasının nasıl çözümleneceği açıklanır. Bu bölgede veya iş gereksinimlerinizi karşılayan alternatif bir bölgede uygun bir SKU bulamıyorsanız Azure desteği 'ne bir [SKU isteği](https://aka.ms/skurestriction) gönderebilirsiniz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Belirti

Bir kaynak (genellikle bir sanal makine) dağıttığınızda aşağıdaki hata kodunu ve hata iletisini alırsınız:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Nedeni

Seçtiğiniz kaynak SKU 'SU (VM boyutu gibi) seçtiğiniz konum için kullanılabilir olmadığında bu hatayı alırsınız.

## <a name="solution-1---powershell"></a>Çözüm 1 - PowerShell

Bir bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) komutunu kullanın. Sonuçları konuma göre filtreleyin. Bu komut için en son PowerShell sürümüne sahip olmanız gerekir.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Sonuçlar, konum ve bu SKU için herhangi bir kısıtlama için SKU 'ların bir listesini içerir. SKU 'nun `NotAvailableForSubscription`olarak listelendiğine dikkat edin.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Çözüm 2 - Azure CLI

Bir bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için `az vm list-skus` komutunu kullanın. Kullandığınız konuma çıktıyı filtrelemek için `--location` parametresini kullanın. Kısmi bir boyut adına göre aramak için `--size` parametresini kullanın.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Komut şunun gibi sonuçlar döndürür:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Çözüm 3 - Azure portalı

Bir bölgede hangi SKU 'Ların kullanılabildiğini öğrenmek için [portalını](https://portal.azure.com)kullanın. Portalda oturum açın ve arabirim aracılığıyla bir kaynak ekleyin. Değerleri ayarladığınız sürece bu kaynak için kullanılabilir SKU 'Ları görürsünüz. Dağıtımı doldurmanız gerekmez.

Örneğin, bir sanal makine oluşturma işlemini başlatın. Kullanılabilir diğer boyutu görmek için **boyutu Değiştir**' i seçin.

![VM oluşturma](./media/resource-manager-sku-not-available-errors/create-vm.png)

Kullanılabilir boyutlarda filtre uygulayabilir ve bu boyutları kaydırabilirsiniz.

![Kullanılabilir SKU 'Lar](./media/resource-manager-sku-not-available-errors/available-sizes.png)

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


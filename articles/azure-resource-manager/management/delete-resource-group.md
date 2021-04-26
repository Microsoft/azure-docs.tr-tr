---
title: Kaynak grubunu ve kaynakları Sil
description: Kaynak gruplarının ve kaynakların nasıl silineceğini açıklar. Bir kaynak grubunu silerken kaynakların silme işlemini nasıl Azure Resource Manager belirtir. Yanıt kodlarını ve Kaynak Yöneticisi silmenin başarılı olup olmadığını belirlemede bunların nasıl işlediğini açıklar.
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 3c062c2f775e145347129f24b201748ee517daf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768678"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager kaynak grubu ve kaynak silme

Bu makalede, kaynak gruplarının ve kaynakların nasıl silineceği gösterilir. Bir kaynak grubunu sildiğinizde kaynakların silme işlemini nasıl Azure Resource Manager belirtir.

## <a name="how-order-of-deletion-is-determined"></a>Silme sırası nasıl belirlenir

Bir kaynak grubunu sildiğinizde Kaynak Yöneticisi kaynakların silineceği sırayı belirler. Aşağıdaki sırayı kullanır:

1. Tüm alt (iç içe) kaynaklar silinir.

2. Diğer kaynakları yöneten kaynaklar daha sonra silinir. Bir kaynak, `managedBy` farklı bir kaynağın bunu yönettiğini göstermek için özelliği ayarlanmış olabilir. Bu özellik ayarlandığında, diğer kaynağı yöneten kaynak diğer kaynaklardan önce silinir.

3. Kalan kaynaklar önceki iki kategoriden sonra silinir.

Sıra saptandıktan sonra, Kaynak Yöneticisi her kaynak için SILME işlemini yayınlar. Devam etmeden önce tüm bağımlılıkların bitmesini bekler.

Zaman uyumlu işlemler için beklenen başarılı yanıt kodları şunlardır:

* 200
* 204
* 404

Zaman uyumsuz işlemler için beklenen başarılı yanıt 202 ' dir. Kaynak Yöneticisi, zaman uyumsuz silme işleminin durumunu öğrenmek için konum üst bilgisini veya Azure-Async işlem üst bilgisini izler.
  
### <a name="deletion-errors"></a>Silme işlemi hataları

Silme işlemi bir hata döndürdüğünde, Kaynak Yöneticisi SILME çağrısını yeniden dener. 5 xx, 429 ve 408 durum kodları için yeniden denemeler meydana gelir. Varsayılan olarak, yeniden deneme için süre 15 dakikadır.

## <a name="after-deletion"></a>Silinmeden sonra

Kaynak Yöneticisi, silmeye çalıştığı her kaynak üzerinde bir GET çağrısı yayınlar. Bu GET çağrısının yanıtının 404 olması beklenir. Kaynak Yöneticisi bir 404 aldığında, silme işleminin başarıyla tamamlandığını kabul eder. Kaynak Yöneticisi, kaynağı önbelleğinden kaldırır.

Ancak, kaynaktaki GET çağrısı bir 200 veya 201 döndürürse Kaynak Yöneticisi kaynağı yeniden oluşturur.

GET işlemi bir hata döndürürse Kaynak Yöneticisi aşağıdaki hata kodu için Al işlemini yeniden dener:

* 100 'den az
* 408
* 429
* 500 'den büyük

Diğer hata kodları için Kaynak Yöneticisi kaynağı silme işlemi başarısız olur.

> [!IMPORTANT]
> Kaynak grubu silme işlemi geri alınamaz.

## <a name="delete-resource-group"></a>Kaynak grubunu silme

Kaynak grubunu silmek için aşağıdaki yöntemlerden birini kullanın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Portalda](https://portal.azure.com), silmek istediğiniz kaynak grubunu seçin.

1. **Kaynak grubunu sil**'i seçin.

   ![Kaynak grubunu silme](./media/delete-resource-group/delete-group.png)

1. Silmeyi onaylamak için kaynak grubunun adını yazın

---

## <a name="delete-resource"></a>Kaynağı Sil

Bir kaynağı silmek için aşağıdaki yöntemlerden birini kullanın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Portalda](https://portal.azure.com), silmek istediğiniz kaynağı seçin.

1. **Sil**’i seçin. Aşağıdaki ekran görüntüsünde, bir sanal makine için yönetim seçenekleri gösterilmektedir.

   ![Kaynağı Sil](./media/delete-resource-group/delete-resource.png)

1. Sorulduğunda silme işlemini onaylayın.

---

## <a name="required-access"></a>Gerekli erişim

Bir kaynak grubunu silmek için, **Microsoft. resources/abonelikler/resourceGroups** kaynağı için silme eylemine erişmeniz gerekir. Kaynak grubundaki tüm kaynaklar için de silmeniz gerekir.

İşlemler listesi için bkz. [Azure Kaynak sağlayıcısı işlemleri](../../role-based-access-control/resource-provider-operations.md). Yerleşik rollerin bir listesi için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md).

Gerekli erişime sahipseniz, ancak silme isteği başarısız olursa, bunun nedeni kaynak grubunda bir [kilit](lock-resources.md) olması olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* Kaynak Yöneticisi kavramları anlamak için bkz. [Azure Resource Manager genel bakış](overview.md).
* Silme komutları için bkz. [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group#az_group_delete)ve [REST API](/rest/api/resources/resourcegroups/delete).

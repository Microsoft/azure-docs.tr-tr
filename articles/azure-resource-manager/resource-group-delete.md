---
title: Kaynak grubunu ve kaynakları Sil
description: Kaynak gruplarının ve kaynakların nasıl silineceğini açıklar. Bir kaynak grubunu silerken kaynakların silme işlemini nasıl Azure Resource Manager belirtir. Bu, yanıt kodları ve Resource Manager silme başarılı olup olmadığını belirlemek için bunları nasıl işlediğini açıklar.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: c84dcf64555f40f45dbdaeedb05d4cc461f2cbb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150821"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager kaynak grubu ve kaynak silme

Bu makalede, kaynak gruplarının ve kaynakların nasıl silineceği gösterilir. Bir kaynak grubunu sildiğinizde kaynakların silme işlemini nasıl Azure Resource Manager belirtir.

## <a name="how-order-of-deletion-is-determined"></a>Silme sırası nasıl belirlenir

Bir kaynak grubu sildiğinizde, Resource Manager kaynakları silmek için sırasını belirler. Aşağıdaki sırayı kullanır:

1. Tüm alt (iç içe) kaynaklar silinir.

2. Diğer kaynakları yönetmek kaynaklar ardından silinir. Bir kaynak olabilir `managedBy` farklı bir kaynak, yönettiği belirtmek için özelliğini ayarlayın. Bu özelliği ayarlandığında, diğer kaynak yöneten kaynak önce diğer kaynaklar silinir.

3. Önceki iki kategoriye sonra kalan kaynaklar silinir.

Sırasını belirlendikten sonra kaynak yöneticisi her kaynak için bir silme işlemi verir. Herhangi bir bağımlılığın devam etmeden önce tamamlanmasını bekler.

Zaman uyumlu işlemler için beklenen başarılı yanıt kodları şunlardır:

* 200
* 204
* 404

Zaman uyumsuz işlemleri için beklenen başarılı yanıt, 202. Kaynak Yöneticisi konum veya silme zaman uyumsuz işlemin durumunu belirlemek için azure-zaman uyumsuz işlemi başlığındaki izler.
  
### <a name="deletion-errors"></a>Silme işlemi hataları

Bir hata silme işlemi geri döndüğünde, Resource Manager DELETE çağrısı yeniden dener. Yeniden denemeler için 429 ve 408 durum kodu 5xx gerçekleşir. Varsayılan olarak, yeniden deneme süre 15 dakikadır.

## <a name="after-deletion"></a>Silindikten sonra

Kaynak Yöneticisi bir GET çağrısı silmeye çalıştığınız her kaynaktaki verir. Yanıtını bu alma çağrısı, 404 olması beklenir. Resource Manager bir 404 girdiğinde, başarıyla tamamladınız silme göz önünde bulundurur. Resource Manager kaynak önbelleğinden kaldırır.

Ancak, kaynaktaki alma çağrısı, bir 200 ya da 201 döndürürse, Resource Manager kaynak yeniden oluşturur.

GET işlemi hata verirse, Resource Manager aşağıdaki hata kodunu alma yeniden deneme:

* 100’den daha az
* 408
* 429
* 500'den büyük

Diğer hata kodları için Resource Manager kaynak silme işlemi başarısız olur.

## <a name="delete-resource-group"></a>Kaynak grubunu silme

Kaynak grubunu silmek için aşağıdaki yöntemlerden birini kullanın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. [Portalda](https://portal.azure.com), silmek istediğiniz kaynak grubunu seçin.

1. **Kaynak grubunu sil**'i seçin.

   ![Kaynak grubunu silme](./media/resource-group-delete/delete-group.png)

1. Silmeyi onaylamak için kaynak grubunun adını yazın

---

## <a name="delete-resource"></a>Kaynağı Sil

Bir kaynağı silmek için aşağıdaki yöntemlerden birini kullanın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. [Portalda](https://portal.azure.com), silmek istediğiniz kaynağı seçin.

1. **Sil**’i seçin. Aşağıdaki ekran görüntüsünde, bir sanal makine için yönetim seçenekleri gösterilmektedir.

   ![Kaynağı Sil](./media/resource-group-delete/delete-resource.png)

1. Sorulduğunda silme işlemini onaylayın.

---


## <a name="next-steps"></a>Sonraki adımlar

* Resource Manager kavramları anlamak için bkz. [Azure Resource Manager'a genel bakış](resource-group-overview.md).
* Silme komutlar için bkz [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete), ve [REST API](/rest/api/resources/resourcegroups/delete).

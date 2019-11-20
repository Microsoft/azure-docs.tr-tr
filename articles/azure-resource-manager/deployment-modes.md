---
title: Dağıtım modları
description: Azure Resource Manager ile tamamlanmış veya artımlı dağıtım modunun kullanılıp kullanılmayacağını nasıl belirleyeceğiniz açıklanır.
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 44111787736d4b80cbdd68263b137d67de2218ba
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149884"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager Dağıtım modları

Kaynaklarınızı dağıttığınızda, dağıtımın bir artımlı güncelleştirme ya da tamamen güncelleştirme olduğunu belirtirsiniz.  Bu iki mod arasındaki fark, Kaynak Yöneticisi şablonda olmayan kaynak grubunda var olan kaynakları nasıl işleyeceğinden yapılır. Varsayılan mod artımlı ' dır.

Her iki mod için Kaynak Yöneticisi şablonda belirtilen tüm kaynakları oluşturmaya çalışır. Kaynak, kaynak grubunda zaten varsa ve ayarları değişmezse, bu kaynak için hiçbir işlem yapılmaz. Bir kaynağın özellik değerlerini değiştirirseniz, kaynak bu yeni değerlerle güncelleştirilir. Mevcut bir kaynağın konumunu veya türünü güncelleştirmeye çalışırsanız, dağıtım bir hata ile başarısız olur. Bunun yerine, gereken konuma veya türe sahip yeni bir kaynak dağıtın.

## <a name="complete-mode"></a>Mod Tamam

Tüm modda Kaynak Yöneticisi kaynak grubunda var olan ancak şablonda belirtilmeyen kaynakları **siler** .

Şablonunuz, [koşul](conditional-resource-deployment.md) false olarak değerlendirildiği için dağıtılan bir kaynağı içeriyorsa, sonuç, şablonu dağıtmak için kullandığınız REST API sürümüne bağlıdır. 2019-05-10 'den önceki bir sürümü kullanıyorsanız, kaynak **silinmez**. 2019-05-10 veya sonraki bir sürümü kullanarak kaynak **silinir**. Azure PowerShell ve Azure CLı 'nın en son sürümleri, kaynağı siler.

[Kopyalama döngülerine](resource-group-create-multiple.md)sahip tüm modu kullanırken dikkatli olun. Kopyalama döngüsü çözümlendikten sonra şablonda belirtilmeyen kaynaklar silinir.

[Bir şablonda birden fazla kaynak grubuna](resource-manager-cross-resource-group-deployment.md)dağıtırsanız, dağıtım işleminde belirtilen kaynak grubundaki kaynaklar silinebilir. İkincil kaynak gruplarındaki kaynaklar silinmez.

Kaynak türlerinin tamamlanma modu silme işlemlerinin nasıl ele aldığı bazı farklılıklar vardır. Üst kaynaklar, tamamlanmış modda dağıtılan bir şablonda olmadığında otomatik olarak silinir. Bazı alt kaynaklar şablonda olmadığında otomatik olarak silinmez. Ancak, üst kaynak silinirse bu alt kaynaklar silinir. 

Örneğin, kaynak grubunuz bir DNS bölgesi (Microsoft. Network/dnsZones kaynak türü) ve CNAME kaydı (Microsoft. Network/dnsZones/CNAME kaynak türü) içeriyorsa DNS bölgesi CNAME kaydı için üst kaynaktır. ' İ tüm moduyla dağıtıp DNS bölgesini şablonunuza eklemezseniz, DNS bölgesinin ve CNAME kaydının ikisi de silinir. Şablonunuzda DNS bölgesini dahil ederseniz ancak CNAME kaydını eklemezseniz CNAME silinmez. 

Kaynak türlerinin silinme işleminin nasıl yapıldığını gösteren bir liste için, bkz. [tüm mod dağıtımları Için Azure kaynaklarını silme](complete-mode-deletion.md).

Kaynak grubu [kilitliyse](resource-group-lock-resources.md), tamamlanmış mod kaynakları silmez.

> [!NOTE]
> Yalnızca kök düzeyindeki şablonlar, tam Dağıtım modunu destekler. [Bağlantılı veya iç içe şablonlar](resource-group-linked-templates.md)için artımlı Mod kullanmanız gerekir. 
>
> [Abonelik düzeyindeki dağıtımlar](deploy-to-subscription.md) , tamamlanma modunu desteklemez.
>
> Şu anda portal, tamamlanmış modunu desteklemez.
>

## <a name="incremental-mode"></a>Artımlı mod

Artımlı modda Kaynak Yöneticisi, kaynak grubunda var olan ancak şablonda belirtilmeyen, **değiştirilmemiş kaynakları bırakır** .

Ancak, var olan bir kaynağı artımlı modda yeniden dağıttığınızda, sonuç farklıdır. Yalnızca güncelleştirdikleriniz değil, kaynağın tüm özelliklerini belirtin. Yaygın bir yanıltıcı, belirtilmeyen özelliklerin değiştirilmemiş olduğunu düşündüğmeniz. Belirli özellikleri belirtmezseniz, Kaynak Yöneticisi güncelleştirmeyi bu değerlerin üzerine yazarak yorumlar.

## <a name="example-result"></a>Örnek sonuç

Artımlı ve tamamlanmış modlar arasındaki farkı göstermek için aşağıdaki senaryoyu göz önünde bulundurun.

**Kaynak grubu** şunları içerir:

* A kaynağı
* Kaynak B
* Kaynak C

**Şablon** şunları içerir:

* A kaynağı
* Kaynak B
* Kaynak D

**Artımlı** modda dağıtıldığında, kaynak grubu:

* A kaynağı
* Kaynak B
* Kaynak C
* Kaynak D

**Tüm** modda dağıtıldığında, kaynak C silinir. Kaynak grubu:

* A kaynağı
* Kaynak B
* Kaynak D

## <a name="set-deployment-mode"></a>Dağıtım modunu ayarla

PowerShell ile dağıtma sırasında dağıtım modunu ayarlamak için `Mode` parametresini kullanın.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Azure CLı ile dağıtma sırasında dağıtım modunu ayarlamak için `mode` parametresini kullanın.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Aşağıdaki örnek, artımlı dağıtım moduna ayarlanmış bir bağlı şablonu gösterir:

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Sonraki adımlar

* Kaynak Yöneticisi şablonları oluşturma hakkında bilgi edinmek için bkz. [yazma Azure Resource Manager şablonları](resource-group-authoring-templates.md).
* Kaynakları dağıtma hakkında bilgi edinmek için bkz. [Azure Resource Manager şablonuyla uygulama dağıtma](resource-group-template-deploy.md).
* Bir kaynak sağlayıcısına yönelik işlemleri görüntülemek için bkz. [Azure REST API](/rest/api/).

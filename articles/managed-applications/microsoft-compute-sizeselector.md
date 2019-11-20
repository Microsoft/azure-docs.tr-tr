---
title: Azure SizeSelector Kullanıcı arabirimi öğesi | Microsoft Docs
description: Azure portal için Microsoft. COMPUTE. SizeSelector Kullanıcı arabirimi öğesini açıklar. Bir sanal makinenin boyutunu seçmek için kullanın.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: f3a8426856e1345306acff69946beb4860d5f905
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151526"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft. COMPUTE. SizeSelector Kullanıcı arabirimi öğesi

Bir veya daha fazla sanal makine örneği için boyut seçmeye yönelik bir denetim.

## <a name="ui-sample"></a>UI örneği

Kullanıcı, öğe tanımından varsayılan değerlerle bir seçici görüyor.

![Microsoft. COMPUTE. SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Denetim seçildikten sonra Kullanıcı, kullanılabilir boyutların genişletilmiş bir görünümünü görür.

![Microsoft. COMPUTE. SizeSelector genişletildi](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
"Standard_D1"
```

## <a name="remarks"></a>Açıklamalar

- `recommendedSizes` en az bir boyuta sahip olmalıdır. Önerilen ilk boyut varsayılan olarak kullanılır. Kullanılabilir boyutların listesi önerilen duruma göre sıralanmaz. Kullanıcı önerilen duruma göre sıralamak için bu sütunu seçebilir.
- Seçilen konumda önerilen bir boyut yoksa, boyut otomatik olarak atlanır. Bunun yerine önerilen sonraki boyut kullanılır.
- `constraints.allowedSizes` ve `constraints.excludedSizes` her ikisi de isteğe bağlıdır, ancak aynı anda kullanılamaz. Kullanılabilir boyutların listesi, [bir abonelik için kullanılabilir sanal makine boyutları](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)çağırarak belirlenebilir. `constraints.allowedSizes` belirtilen herhangi bir boyut gizlidir ve `constraints.excludedSizes` belirtilen tüm boyut gösterilir.
- `osPlatform` belirtilmelidir ve **Windows** ya da **Linux**olabilir. Bu, sanal makinelerin donanım maliyetlerini belirlemede kullanılır.
- `imageReference`, birinci taraf görüntüleri için atlanır, ancak üçüncü taraf görüntüler için sağlanır. Bu, sanal makinelerin Yazılım maliyetlerini belirlemede kullanılır.
- `count`, öğe için uygun çarpanı ayarlamak için kullanılır. **2**gibi bir statik değeri veya `[steps('step1').vmCount]`gibi başka bir öğeden dinamik bir değeri destekler. Varsayılan değer **1**' dir.
- `numAvailabilityZonesRequired` 1, 2 veya 3 olabilir.
- Varsayılan olarak, `hideDiskTypeFilter` **false 'tur**. Disk türü filtresi, kullanıcının tüm disk türlerini veya yalnızca SSD 'yi görmesini sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).

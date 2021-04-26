---
author: baanders
description: Azure Digital TWINS için dosya ekleme-komut satırı öğreticisi için model yönergeleri
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463928"
---
1. Bu modelin daha güncel bir sürümünü sağladıklarını belirtmek için **sürüm numarasını güncelleştirin**. Değerin sonundaki *1* `@id` değerini *2* olarak değiştirerek bunu yapın. Geçerli sürüm numarasından daha büyük bir sayı de çalışacaktır.
1. **Bir özelliği düzenleyin**. `Humidity`Özelliğin adını *Humidtylevel* (veya isterseniz farklı bir şey) olarak değiştirin. *Humidtylevel*' dan farklı bir şey kullanırsanız, kullandığınız şeyi hatırlayın ve öğretici genelinde *Humidtylevel* yerine bunu kullanmaya devam edin).
1. **Özellik ekleyin**. 15. `HumidityLevel` satırda sonlanan özelliğin altında, Odaya bir özellik eklemek için aşağıdaki kodu yapıştırın `RoomName` :

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Ilişki ekleyin**. `RoomName`Az önce eklediğiniz özelliğin altında, bu tür bir ikizi oluşturmak için aşağıdaki kodu yapıştırın. 

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

İşiniz bittiğinde, güncelleştirilmiş modelin eşleşmesi gerekir:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Taşımadan önce dosyayı kaydettiğinizden emin olun.
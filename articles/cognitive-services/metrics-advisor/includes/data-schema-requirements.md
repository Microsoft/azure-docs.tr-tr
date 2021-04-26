---
title: Veri şeması gereksinimleri
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 4c55c25621df1925b6ed6c374d8af88551eb1e46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96231438"
---
Ölçüm Danışmanı, zaman serisi anomali algılama, tanılama ve analizler için bir hizmettir. AI destekli bir hizmet olarak, kullanılan modeli eğitebilmeniz için verilerinizi kullanır. Hizmet, toplanan verilerin tablolarını aşağıdaki sütunlara sahip olarak kabul eder:

* **Ölçü** (zorunlu): sayısal değerler içeren bir veya daha fazla sütun.
* **Zaman damgası** (isteğe bağlı): sıfır veya türü olan bir `DateTime` sütun `String` . Bu sütun ayarlanmamışsa, zaman damgası her alma döneminin başlangıç saati olarak ayarlanır. Zaman damgasını şu şekilde biçimlendirin: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **Zaman Damgalarınız, ölçümün ayrıntı düzeyi ile eşleşmelidir. Örneğin, günlük ölçüm, zaman damgasında olarak `00:00:00` etiketlenen saat, dakika ve saniyeyi sağlamalıdır**.
* **Boyut** (isteğe bağlı): sütunlar herhangi bir veri türünde olabilir. Fazla sayıda boyutun işlenmesini engellemek için büyük miktarda sütun ve değerle çalışırken dikkatli olun.

> [!Note]
> Her ölçüm için, tek bir boyut birleşimine karşılık gelen, ölçü başına yalnızca bir zaman damgası olmalıdır. Verilerinizi ekleme öncesinde toplayın veya alınacak verileri belirtmek için sorguyu kullanın.
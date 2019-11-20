---
title: Project Acoustics Bake çözünürlüğü
titlesuffix: Azure Cognitive Services
description: Bu kavramsal genel bakış, Acoustics önemli çözünürlükler arasındaki farkı açıklar.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854359"
---
# <a name="project-acoustics-bake-resolution"></a>Project Acoustics Bake çözünürlüğü
Bu kavramsal genel bakış, Acoustics önemli çözünürlükler arasındaki farkı açıklar. Bu ayarı, fırın iş akışının Yoklame adımı sırasında seçersiniz.

## <a name="Coarse-vs-Fine-Resolution"></a>Kaba ve ince çözünürlük

Kaba ve ince çözünürlük ayarları arasındaki tek fark, benzetimi gerçekleştirilen sıklıktır. İnce, en yüksek bir sıklık olarak yüksek bir sıklık kullanır. Bu, akustik simülasyonu üzerinde bazı etkilere sahiptir:

* Kalın uzunlukla ilgili dalga uzunluğu, doğru uzunluktadır ve bu nedenle voxters, büyük olur.
* Benzetim süresi, Voxel boyutuyla doğrudan ilişkilidir. Bu, ince bir bakışta 16 kat daha hızlı bir şekilde daha hızlı bir şekilde gerçekleşir.
* (Örneğin, kapıların veya pencerelerin) Voxel boyutundan küçük olması benzetimi yapılamıyor. Kaba ayar, bu küçük portalların bazılarının benzetimine neden olabilir; Bu nedenle, çalışma zamanında ses geçirmez. Bu durum, VOX, ' ı görüntüleyerek olup olmadığını görebilirsiniz.
* Düşük simülasyon sıklığı, köşeler ve kenarlar etrafında daha az dağıtılmış olur.
* Ses kaynakları "doldurulmuş" voxkine (geometri içeren voxx) içinde bulunamaz. Bu, ses olmadan sonuçlanır. Ses kaynaklarını, ince ayar kullanılırken daha büyük voxksel nesnelerin içine kalmayacak şekilde yerleştirmek daha zordur.
* Daha büyük voxne, aşağıda gösterildiği gibi portallara intrude. İlk görüntü, büyük bir çözüm kullanılarak aynı Doorway, ikincisi ise kaba bir kullanılarak oluşturulmuştur. Kırmızı işaretlerle gösterildiği gibi, Doorway için ince ayar kullanılarak çok daha az yetkisiz erişim vardır. Mavi çizgi, geometri tarafından tanımlanan Doorway, kırmızı çizgi ise Voxel boyutuyla tanımlanan etkin akustik portaldır. Bu yetkisiz giriş, belirli bir durumda nasıl çalındığında, tam olarak, sahneye ait nesnelerinizin boyut ve konumlarına göre belirlenen ve portalın geometrisi ile nasıl bir satıra göre belirlenir.

![Gerçek zamanlı olmayan bir Doorway doldurma için kalın ekran görüntüsü](media/unreal-coarse-bake.png)

![Gerçek olmayan bir Doorway içinde ince voxunlar ekran görüntüsü](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Sonraki adımlar

[Gerçek zamanlı](unreal-baking.md) veya [Unity](unity-baking.md) eklentilerimizi kullanarak gerçekçi ve iyi çözünürlük ayarları yapın.

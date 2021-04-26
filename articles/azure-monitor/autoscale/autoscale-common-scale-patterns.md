---
title: Ortak otomatik ölçeklendirme desenlerine genel bakış
description: Azure 'da kaynağınızın ölçeğini otomatik olarak ölçeklendirmek için kullanılan bazı yaygın desenleri öğrenin.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: adbfecd05bfd4330e7c7c972cfdb9fe7021eec31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100623893"
---
# <a name="overview-of-common-autoscale-patterns"></a>Ortak otomatik ölçeklendirme desenlerine genel bakış
Bu makalede, Azure 'da kaynağınızı ölçeklendirmek için kullanılan bazı yaygın desenler açıklanmaktadır.

Azure Izleyici otomatik ölçeklendirme yalnızca [Sanal Makine Ölçek Kümeleri](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)ve [API Management Hizmetleri](../../api-management/api-management-key-concepts.md)için geçerlidir.

## <a name="lets-get-started"></a>Başlamanıza izin verir

Bu makalede otomatik ölçeklendirmeyi bildiğiniz varsayılmaktadır. Kaynağınızı [ölçeklendirmek için Buradan çalışmaya][1]başlayın. Yaygın ölçek desenlerinden bazıları aşağıda verilmiştir.

## <a name="scale-based-on-cpu"></a>CPU 'ya göre ölçeklendirin

Bir Web uygulamanız (/VMSS/cloud Service Role) ve

- CPU 'ya göre ölçeği genişletmek/ölçeklendirmek istiyorsunuz.
- Ayrıca, en az sayıda örnek olduğundan emin olmak istersiniz.
- Ayrıca, ölçeklendirebilmeniz gereken örnek sayısına bir üst sınır ayarlamış olduğunuzdan emin olmak isteyebilirsiniz.

![CPU 'ya göre ölçeklendirin][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Haftanın haftaları ile farklı ölçekleme

Bir Web uygulamanız (/VMSS/cloud Service Role) ve

- Varsayılan olarak 3 örnek istiyorsunuz (hafta içi)
- Hafta sonları üzerinde trafik beklenmez ve bu nedenle hafta sonları üzerinde 1 örneğe kadar ölçeği azaltabilirsiniz.

![Haftanın haftaları ile farklı ölçekleme][3]

## <a name="scale-differently-during-holidays"></a>Tatiller sırasında farklı ölçeklendirin

Bir Web uygulamanız (/VMSS/cloud Service Role) ve

- Varsayılan olarak CPU kullanımına göre ölçeği artırma/azaltma
- Ancak, tatil döneminde (veya işiniz için önemli olan belirli günler), varsayılan değerleri geçersiz kılmak ve elden çıkarmada daha fazla kapasiteye sahip olmak istiyorsunuz.

![Tatiller üzerinde farklı ölçeklendirin][4]

## <a name="scale-based-on-custom-metric"></a>Özel ölçüme göre ölçeklendirin

Arka uca iletişim kuran bir Web ön ucuna ve API katmanına sahipsiniz.

- Ön uçtaki özel olaylara dayalı olarak API katmanını ölçeklendirmek istiyorsunuz (örnek: alışveriş sepetindeki öğelerin sayısına göre kullanıma alma işleminizi ölçeklendirmek istiyorsunuz)

![Özel ölçüme göre ölçeklendirin][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

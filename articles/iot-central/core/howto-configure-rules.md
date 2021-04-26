---
title: Azure IoT Central’da kural ve eylem yapılandırma | Microsoft Docs
description: Bu nasıl yapılır makalesi, Azure IoT Central uygulamanızda telemetri tabanlı kuralları ve eylemleri nasıl yapılandıracağınızı bir Oluşturucu olarak gösterir.
author: vavilla
ms.author: vavilla
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 378a1dbcfbc89cdf9b24dc2490db583f1135b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796797"
---
# <a name="configure-rules"></a>Kuralları yapılandırma

*Bu makale, işleçler, oluşturucular ve yöneticiler için geçerlidir.*

IoT Central kurallar, bağlı cihazlardan etkin olarak izlenen olaylar üzerinde tetiklenen bir özelleştirilebilir yanıt aracı olarak görev yapar. Aşağıdaki bölümlerde kuralların nasıl değerlendirildiği açıklanır.

## <a name="select-target-devices"></a>Hedef cihazları seçin

Bu kuralın ne tür cihazlarda uygulanacağını seçmek için hedef cihazlar bölümünü kullanın. Filtreler, hangi cihazların dahil edileceğini daha da iyileştirmenize olanak tanır. Filtreler cihaz kümesini filtrelemek için cihaz şablonundaki özellikleri kullanır. Filtreler bir eylem tetiklemez. Aşağıdaki ekran görüntüsünde, hedeflenen cihazların cihaz şablonu türü **soğutma**' dir. Filtre, kuralın yalnızca **üretilen durum** özelliği **Washington**'a eşit olan **soğutma** 'yi içermesi gerektiğini belirtir.

![Koşullar](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Birden çok koşul kullanma

Koşullar, üzerinde hangi kuralların tetikleyeceğini de tetikler. Şu anda, bir kurala birden çok koşul eklediğinizde bunlar mantıksal olarak ve Ile birlikte yapılır. Diğer bir deyişle kuralın doğru olarak değerlendirilmesi için tüm koşulların karşılanması gerekir.  

Aşağıdaki ekran görüntüsünde koşullar, sıcaklığın 70 F 'den büyük olduğunu &deg; ve nem 10 ' dan küçük olduğunu denetler. Bu deyimlerden her ikisi de doğru olduğunda, kural true olarak değerlendirilir ve bir eylemi tetikler.

![Ekran görüntüsü sıcaklık ve nem için belirtilen koşullara sahip bir soğutma izlemeyi gösterir.](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Değer alanında bir bulut özelliği kullanın

Koşul için **değer** alanındaki cihaz şablonundan bir bulut özelliğine başvurabilirsiniz. Bulut özelliği ve telemetri değeri benzer türlere sahip olmalıdır. Örneğin, **sıcaklık** bir Double Ise, **değer** açılan kutusunda yalnızca Double olarak göster türündeki bulut özellikleri.

Bir olay türü telemetri değeri seçerseniz, **değer açılır değeri** **herhangi bir** seçeneği içerir. **Any** seçeneği, uygulamanız bu türden bir olay aldığında yükün ne zaman tetiklendiği anlamına gelir.

## <a name="use-aggregate-windowing"></a>Birleşik Pencereleme kullan

Kurallar, Windows 'un aldığı Birleşik zaman pencerelerini değerlendirir. Aşağıdaki ekran görüntüsünde, zaman penceresi beş dakikadır. Her beş dakikada bir kural, son beş dakikalık verileri değerlendirir. Veriler yalnızca bir kez karşılık geldiği pencerede değerlendirilir.

![Atlayan pencereler](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>IoT Edge modüllerle kuralları kullanma

Bir kısıtlama IoT Edge modüllerine uygulanan kurallar için geçerlidir. Farklı modüllerden gelen telemetri kuralları geçerli kurallar olarak değerlendirilmez. Örnek olarak aşağıdakileri yapın. Kuralın ilk koşulu, A modülünden bir sıcaklık telemetrisine sahiptir. Kuralın ikinci koşulu, B modülündeki bir nem telemetrisi üzerinde. İki koşul farklı modüllerden olduğundan, bu geçersiz bir koşullar kümesidir. Kural geçerli değil ve kuralı kaydetmeye çalışırken bir hata oluşturacak.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda bir kuralın nasıl yapılandırılacağını öğrendiğinize göre, Power otomatikleştir veya Azure Logic Apps kullanarak [Gelişmiş kuralların nasıl yapılandırılacağını](howto-configure-rules-advanced.md) öğrenebilirsiniz.

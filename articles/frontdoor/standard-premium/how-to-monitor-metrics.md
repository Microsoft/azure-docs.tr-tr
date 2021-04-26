---
title: Azure ön kapısının Standart/Premium için izleme ölçümleri
description: Bu makalede, Azure ön kapısının Standart/Premium izleme ölçümleri açıklanmaktadır.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 72388eb8006ff1b9628db5066dc63e6a0811f3d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557335"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Azure ön kapısı Standart/Premium 'da gerçek zamanlı Izleme

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Azure ön kapısının Standart/Premium, Azure Izleyici ile tümleşiktir ve Azure ön kapısı Standart/Premium 'u gerçek zamanlı olarak izlemeye, sorun gidermenize ve hata ayıklamanıza yardımcı olmak için 11 ölçülerde bulunur.  

Azure ön kapısının Standart/Premium ölçümleri ve ölçümlerini 60 saniyelik aralıklarla gönderir. Ölçümlerin portalda görünmesi 3 dakika kadar sürebilir. Ölçümler, seçtiğiniz grafiklerde veya kılavuzda görüntülenebilir ve Portal, PowerShell, CLı ve API aracılığıyla erişilebilir. Daha fazla bilgi için bkz. [Azure izleyici ölçümleri](../../azure-monitor/essentials/data-platform-metrics.md).  

Varsayılan ölçümler ücretsizdir. Ek bir maliyet için ek ölçümleri etkinleştirebilirsiniz. 

Her ölçüm için, 4XXErrorRate veya 5XXErrorRate için bir eşik gibi uyarılar yapılandırabilirsiniz. Hata oranı eşiği aşarsa, yapılandırılan şekilde bir uyarı tetikler. Daha fazla bilgi için bkz. [Azure izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../../azure-monitor/alerts/alerts-metric.md). 

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Azure ön kapı Standart/Premium 'da desteklenen ölçümler

| Ölçümler  | Description | Boyutlar |
| ------------- | ------------- | ------------- |
| Bayt Isabet oranı | Toplam çıkış ile hesaplanan AFD önbelleğinden çıkış yüzdesi. </br> **Byte vuruş oranı** = (uçtan çıkış çıkış noktasından çıkış)/kenardan çıkış. </br> **Bayt isabet oranı hesaplamasında dışlanan senaryolar**:</br> 1. bir önbelleği, kural altyapısı veya sorgu dizesi önbellek davranışı aracılığıyla açıkça yapılandırırsınız. </br> 2. Cache-Control Direktifi veya özel önbelleğiyle açıkça yapılandırırsınız. </br>3. trafiğin büyük bir kısmının, yapılandırma veya senaryolarınıza göre önbelleğe alma yerine kaynağa iletilebilmesi için bayt isabet oranı düşük olabilir. | Uç Nokta |
| Istek sayısı | CDN tarafından sunulan istemci isteklerinin sayısı. | Uç nokta, istemci ülkesi, istemci bölgesi, HTTP durumu, HTTP durum grubu |
| Yanıt boyutu | AFD tarafından sunulan istemci isteklerinin sayısı. |Uç nokta, istemci ülkesi, istemci bölgesi, HTTP durumu, HTTP durum grubu |
| TotalLatency | **Son Yanıt BAYTı CDN 'den istemciye gönderilene kadar** CDN tarafından alınan istemci isteğinin toplam süresi. |Uç nokta, istemci ülkesi, istemci bölgesi, HTTP durumu, HTTP durum grubu |
| RequestSize | İstemcilerden AFD 'a istek olarak gönderilen bayt sayısı. | Uç nokta, istemci ülkesi, istemci bölgesi, HTTP durumu, HTTP durum grubu |
| 4XX% ErrorRate | Yanıt durum kodu 4XX olan tüm istemci isteklerinin yüzdesi. | Uç nokta, Istemci ülkesi, Istemci bölgesi |
| 5XX% ErrorRate | Yanıt durum kodu 5 xx olan tüm istemci isteklerinin yüzdesi. | Uç nokta, Istemci ülkesi, Istemci bölgesi |
| OriginRequestCount  | AFD 'dan kaynağa gönderilen isteklerin sayısı | Uç nokta, kaynak, HTTP durumu, HTTP durum grubu |
| OriginLatency | AFD, AFD 'ın, arka ucun son yanıt baytını almayana kadar, AFD Edge tarafından arka uca gönderildiği zaman hesaplanır. | Uç nokta, kaynak |
| OriginHealth% | AFD 'den Origin 'e yapılan başarılı sistem durumu araştırmalarının yüzdesi.| Kaynak, kaynak grubu |
| WAF istek sayısı | Eşleşen WAF isteği. | Eylem, kural adı, Ilke adı |

## <a name="access-metrics-in-azure-portal"></a>Azure portal erişim ölçümleri

1. Azure portal menüsünde **tüm kaynaklar**' ı seçin  >>  **\<your-AFD Standard/Premium (Preview) -profile>** .

2. **İzleme** altında **ölçümler**' i seçin:

3. **Ölçümler**' de eklenecek ölçümü seçin:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Ölçüm sayfasının ekran görüntüsü." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. Filtre eklemek için **Filtre Ekle** ' yi seçin:

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Ölçümlere filtre ekleme ekranının ekran görüntüsü." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Verileri farklı boyutlara göre bölmek için **bölmeyi Uygula** ' yı seçin:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Ölçümlere boyut eklemenin ekran görüntüsü." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Yeni **grafik ' i seçerek** yeni bir grafik ekleyin:

## <a name="configure-alerts-in-azure-portal"></a>Azure portal uyarıları yapılandırma

1. **İzleme** uyarıları ' nı seçerek Azure ön kapısı Standart/Premium 'da (Önizleme) uyarıları ayarlayın  >>  .

1. Ölçümler bölümünde listelenen ölçümler için **Yeni uyarı kuralı** ' nı seçin.

Uyarı, Azure Izleyici 'ye göre ücretlendirilir. Uyarılar hakkında daha fazla bilgi için bkz. [Azure izleyici uyarıları](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ön kapısının Standart/Premium raporları](how-to-reports.md)hakkında bilgi edinin.
- [Azure ön kapısının Standart/Premium günlükleri](how-to-logs.md)hakkında bilgi edinin.
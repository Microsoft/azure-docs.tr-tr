---
title: Azure Application Insights 'de akıllı algılama | Microsoft Docs
description: Application Insights, uygulama telemetrinizin otomatik derin analizini yapar ve olası sorunlar hakkında sizi uyarır.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 974795e853416ff7a4c051530b050fc663c9423d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585656"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights'da Akıllı Algılama
 Akıllı algılama, Web uygulamanızdaki olası performans sorunlarını ve hata bozuklumlarını otomatik olarak uyarır. Uygulamanızın [Application Insights](./app-insights-overview.md)için gönderdiği Telemetriyi proaktif analizini gerçekleştirir. Hata hızlarındaki ani bir artış veya istemci ya da sunucu performansındaki anormal desenler varsa, bir uyarı alırsınız. Bu özelliğin yapılandırması yok. Uygulamanız yeterli telemetri gönderdiğinde çalışır.

Akıllı algılama tarafından verilen Algılamalarda hem aldığınız e-postalardan hem de akıllı algılama dikey penceresinden erişebilirsiniz.

## <a name="review-your-smart-detections"></a>Akıllı algılamalarınızı gözden geçirin
Algılamaları iki şekilde keşfedebilirsiniz:

* Application Insights **bir e-posta alırsınız** . Aşağıda tipik bir örnek verilmiştir:
  
    ![E-posta uyarısı](./media/proactive-diagnostics/03.png)
  
    Portalda daha fazla ayrıntı açmak için büyük düğmesine tıklayın.
* Application Insights içindeki **akıllı algılama dikey** penceresi. Son algılamaların listesini görmek için **Araştır** menüsünde **akıllı algılama** ' yı seçin.

![Son algılamaları görüntüle](./media/proactive-diagnostics/04.png)

Ayrıntılarını görmek için bir algılama seçin.

## <a name="what-problems-are-detected"></a>Hangi sorunlar algılanır?
Akıllı algılama, şu gibi çeşitli sorunları algılar ve bildirir:

* [Akıllı algılama-hata bozuklukları](./proactive-failure-diagnostics.md). Makinenizde, yük ve diğer faktörlerle ilişkili, başarısız isteklerin beklenen oranını ayarlamak için makine öğrenimi kullanıyoruz. Hata oranı beklenen zarfın dışında kaldığında bir uyarı göndereceğiz.
* [Akıllı algılama-performans Bozuklulıkları](./proactive-performance-diagnostics.md). Bir işlemin veya bağımlılık süresinin yanıt süresinin geçmiş taban çizgisine kıyasla veya yanıt süresi ya da sayfa yükleme sırasında anormal bir model tanımlıyoruz bildirimleri alırsınız.   
* Genel azalmaları ve sorunlar, [izleme performansında azalma](./proactive-trace-severity.md), [bellek sızıntısı](./proactive-potential-memory-leak.md), [olağandışı artış, özel durum hacminde](./proactive-exception-volume.md) ve [güvenlik önleme desenlerinde](./proactive-application-security-detection-pack.md).

(Her bildirimdeki yardım bağlantıları sizi ilgili makalelere götürür.)

## <a name="smart-detection-email-notifications"></a>Akıllı algılama e-posta bildirimleri

_Önizleme_ olarak işaretlenen kurallar dışında tüm akıllı algılama kuralları, algılamalar bulunduğunda e-posta bildirimleri göndermek için varsayılan olarak yapılandırılır.

Belirli bir akıllı algılama kuralı için e-posta bildirimlerini yapılandırmak, akıllı algılama **ayarları** dikey penceresi açılarak ve kural **düzenleme** dikey penceresini açacak olan kuralı seçerek yapılabilir.

Alternatif olarak, Azure Resource Manager şablonları kullanarak yapılandırmayı değiştirebilirsiniz. Daha fazla ayrıntı için [bkz. Azure Resource Manager şablonları kullanarak Application Insights akıllı algılama kurallarını yönetme](./proactive-arm-config.md) .

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Sonraki adımlar
Bu tanılama araçları uygulamanızdan Telemetriyi incelemenize yardımcı olur:

* [Ölçüm Gezgini](../essentials/metrics-charts.md)
* [Arama Gezgini](./diagnostic-search.md)
* [Analytics-güçlü sorgu dili](../logs/log-analytics-tutorial.md)

Akıllı algılama tamamen otomatiktir. Ancak daha fazla uyarı kurmak istiyor olabilirsiniz?

* [El ile yapılandırılmış ölçüm uyarıları](../alerts/alerts-log.md)
* [Kullanılabilirlik web testleri](./monitor-web-app-availability.md)
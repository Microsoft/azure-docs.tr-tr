---
title: Azure Application Insights 'de akıllı algılama | Microsoft Docs
description: Application Insights, uygulama telemetrinizin otomatik derin analizini yapar ve olası sorunlar hakkında sizi uyarır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/07/2019
ms.openlocfilehash: ae9af5721e53277f6c939840721ca50ea67ca51e
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818823"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights 'de akıllı algılama
 Akıllı algılama, Web uygulamanızdaki olası performans sorunlarını ve hata bozuklumlarını otomatik olarak uyarır. Uygulamanızın [Application Insights](../../azure-monitor/app/app-insights-overview.md)için gönderdiği Telemetriyi proaktif analizini gerçekleştirir. Hata hızlarındaki ani bir artış veya istemci ya da sunucu performansındaki anormal desenler varsa, bir uyarı alırsınız. Bu özelliğin yapılandırması yok. Uygulamanız yeterli telemetri gönderdiğinde çalışır.

Akıllı algılama tarafından verilen Algılamalarda hem aldığınız e-postalardan hem de akıllı algılama dikey penceresinden erişebilirsiniz.

## <a name="review-your-smart-detections"></a>Akıllı algılamalarınızı gözden geçirin
Algılamaları iki şekilde keşfedebilirsiniz:

* Application Insights **bir e-posta alırsınız** . Tipik bir örnek aşağıda verilmiştir:
  
    ![E-posta uyarısı](./media/proactive-diagnostics/03.png)
  
    Portalda daha fazla ayrıntı açmak için büyük düğmesine tıklayın.
* Application Insights içindeki **akıllı algılama dikey** penceresi. Son algılamaların listesini görmek için **Araştır** menüsünde **akıllı algılama** ' yı seçin.

![Son algılamaları görüntüle](./media/proactive-diagnostics/04.png)

Ayrıntılarını görmek için bir algılama seçin.

## <a name="what-problems-are-detected"></a>Hangi sorunlar algılanır?
Akıllı algılama, şu gibi çeşitli sorunları algılar ve bildirir:

* [Akıllı algılama-hata bozuklukları](../../azure-monitor/app/proactive-failure-diagnostics.md). Makinenizde, yük ve diğer faktörlerle ilişkili, başarısız isteklerin beklenen oranını ayarlamak için makine öğrenimi kullanıyoruz. Hata oranı beklenen zarfın dışında kaldığında bir uyarı göndereceğiz.
* [Akıllı algılama-performans Bozuklulıkları](../../azure-monitor/app/proactive-performance-diagnostics.md). Bir işlemin veya bağımlılık süresinin yanıt süresinin geçmiş taban çizgisine kıyasla veya yanıt süresi ya da sayfa yükleme sırasında anormal bir model tanımlıyoruz bildirimleri alırsınız.   
* Genel azaltılmaları ve sorunlar, [izleme](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [bellek sızıntısı](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), olağandışı artış, [özel durum hacminde](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) ve [güvenlik koruması düzenleri](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack)gibi sorunlar.

(Her bildirimdeki yardım bağlantıları sizi ilgili makalelere götürür.)

## <a name="smart-detection-email-notifications"></a>Akıllı algılama e-posta bildirimleri

_Önizleme_olarak işaretlenen kurallar dışında tüm akıllı algılama kuralları, algılamalar bulunduğunda e-posta bildirimleri göndermek için varsayılan olarak yapılandırılır.

Belirli bir akıllı algılama kuralı için e-posta bildirimlerini yapılandırmak, akıllı algılama **ayarları** dikey penceresi açılarak ve kural **düzenleme** dikey penceresini açacak olan kuralı seçerek yapılabilir.

Alternatif olarak, Azure Resource Manager şablonları kullanarak yapılandırmayı değiştirebilirsiniz. Daha fazla ayrıntı için [bkz. Azure Resource Manager şablonları kullanarak Application Insights akıllı algılama kurallarını yönetme](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) .

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Sonraki adımlar
Bu tanılama araçları uygulamanızdan Telemetriyi incelemenize yardımcı olur:

* [Ölçüm Gezgini](../../azure-monitor/app/metrics-explorer.md)
* [Arama Gezgini](../../azure-monitor/app/diagnostic-search.md)
* [Analytics-güçlü sorgu dili](../../azure-monitor/log-query/get-started-portal.md)

Akıllı algılama tamamen otomatiktir. Ancak daha fazla uyarı kurmak istiyor olabilirsiniz?

* [El ile yapılandırılmış ölçüm uyarıları](../../azure-monitor/app/alerts.md)
* [Kullanılabilirlik Web testleri](../../azure-monitor/app/monitor-web-app-availability.md) 


---
title: Azure API Management Azure Application Insights tümleştirme
titleSuffix: Azure API Management
description: Azure Application Insights 'de Azure API Management olaylarını günlüğe kaydetme ve görüntüleme hakkında bilgi edinin.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564261"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure API Management'ı Azure Application Insights ile Tümleştirme

Azure API Management, birden çok platformda uygulama oluşturup yöneten web geliştiricileri için genişletilebilir bir hizmet olan Azure Application Insights ile kolay tümleştirme sağlar. Bu kılavuzda, bu tür tümleştirmenin her adımında izlenecek yol gösterilmektedir ve API Management hizmet örneğiniz üzerindeki performans etkisini azaltmaya yönelik stratejiler açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzu izlemek için bir Azure API Management örneğine sahip olmanız gerekir. Henüz bir hesabınız yoksa, önce [öğreticiyi](get-started-create-service-instance.md) doldurun.

## <a name="create-an-application-insights-instance"></a>Application Insights örneği oluşturma

Application Insights kullanabilmeniz için önce hizmetin bir örneğini oluşturmanız gerekir. Azure portal kullanarak bir örnek oluşturma adımları için bkz. [çalışma alanı tabanlı Application Insights kaynakları](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Application Insights ile API Management arasında bağlantı oluşturma

1. **Azure Portal** **Azure API Management hizmet örneğinize** gidin.
1. Soldaki menüden **Application Insights** seçin.
1. **+ Ekle**'ye tıklayın.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Yeni bir bağlantının nereye ekleneceğini gösteren ekran görüntüsü":::
1. Daha önce oluşturulan **Application Insights** örneğini seçin ve kısa bir açıklama sağlayın.
1. **Oluştur**’a tıklayın.
1. Yalnızca bir izleme anahtarı olan bir Application Insights günlükçüsü oluşturdunuz. Artık listede görünmelidir.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="İzleme anahtarıyla yeni oluşturulan Application Insights günlükçüsü 'nin nerede görüntüleneceği gösteren ekran görüntüsü":::

> [!NOTE]
> Sahnenin arkasında, Application Insights örneğinin Izleme anahtarını içeren API Management örneğiniz için bir [günlükçü](/rest/api/apimanagement/2019-12-01/logger/createorupdate) varlığı oluşturulur.

## <a name="enable-application-insights-logging-for-your-api"></a>API 'niz için Application Insights günlüğe kaydetmeyi etkinleştirme

1. **Azure Portal** **Azure API Management hizmet örneğinize** gidin.
1. Soldaki menüden **API'ler** seçeneğini belirleyin.
1. Bu örnekte, API 'nize tıklayarak bu örnek **Tanıtım Konferansı API 'si**. Yapılandırıldıysa, bir sürüm seçin.
1. Üstteki çubukta **Ayarlar** sekmesine gidin.
1. **Tanılama günlükleri** bölümüne gidin.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="App Insights günlükçüsü":::
1. **Etkinleştir** kutusunu işaretleyin.
1. **Hedef** açılan menüsünde, eklenen günlükçüyü seçin.
1. **Örnekleme (%)** olarak **100** girin ve **her zaman günlük hataları** onay kutusunu seçin.
1. **Kaydet**’i seçin.

> [!WARNING]
> **Yük baytlarının günlüğe kaydedilecek** **0** varsayılan değerini geçersiz kılmak, API 'nizin performansını önemli ölçüde azaltabilir.

> [!NOTE]
> Sahnenin arkasında, API düzeyinde ' ApplicationInsights ' adlı bir [Tanılama](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) varlığı oluşturulur.

| Ayar adı                        | Değer türü                        | Description                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Etkinleştir                              | boolean                           | Bu API 'nin günlüğe kaydetmenin etkinleştirilip etkinleştirilmeyeceğini belirtir.                                                                                                                                                                                                                                                                                                |
| Hedef                         | Azure Application Insights günlükçüsü | Kullanılacak Azure Application Insights günlükçüsü belirtir                                                                                                                                                                                                                                                                                           |
| Örnekleme (%)                        | decimal                           | 0 ile 100 (yüzde) arasında değerler. <br/> Application Insights için günlüğe kaydedilecek isteklerin yüzdesini belirtir. %0 örnekleme, %100 örnekleme, günlüğe kaydedilen tüm isteklerin olduğu anlamına gelen sıfır istek günlüğe kaydedilir. <br/> Application Insights istekleri günlüğe kaydederken performans üzerindeki etkiyi azaltmak için bu ayarı kullanın. Bkz. [performans etkileri ve günlük örneklemesi](#performance-implications-and-log-sampling). |
| Her zaman günlüğe kaydetme hataları                   | boolean                           | Bu ayar seçilirse, **örnekleme** ayarından bağımsız olarak tüm arızalar Application Insights kaydedilir.   
| Günlük istemcisi IP adresi | |  Bu ayar seçilirse, API istekleri için istemci IP adresi Application Insights kaydedilir.                                         |
| Ayrıntı Düzeyi         |                                   | Ayrıntı düzeyini belirtir. Yalnızca daha yüksek önem düzeyine sahip özel izlemeler günlüğe kaydedilir. Varsayılan: bilgi.      | 
| Bağıntı protokolü |  |  Birden çok bileşen tarafından gönderilen Telemetriyi ilişkilendirmek için kullanılan protokolü seçin. Varsayılan: eski <br/>Bilgi için bkz. [Application Insights telemetri bağıntısı](../azure-monitor/app/correlation.md).  |
| Temel seçenekler: günlüğe kaydedilecek üst bilgiler              | list                              | İstekler ve yanıtlar için Application Insights için günlüğe kaydedilecek üst bilgileri belirtir.  Varsayılan: günlüğe hiçbir üst bilgi kaydedilmez.                                                                                                                                                                                                             |
| Temel seçenekler: günlüğe kaydedilecek yük baytlarının sayısı | tamsayı                           | İsteklerin ve yanıtların Application Insights için gövdenin kaç tane ilk baytından kaydedildiğini belirtir.  Varsayılan: 0.                                                                                                                                                                                                    |
| Gelişmiş Seçenekler: ön uç Isteği  |                                   | *Ön uç isteklerinin* Application Insights için günlüğe kaydedilip edilmeyeceğini belirtir. *Ön uç isteği* , Azure API Management hizmetine gelen bir isteğidir.                                                                                                                                                                        |
| Gelişmiş Seçenekler: ön uç yanıtı |                                   | Application Insights için *ön uç yanıtlarının* kaydedilip kaydedilmeyeceğini belirtir. *Ön uç yanıtı* , Azure API Management hizmetinden giden bir yanıttır.                                                                                                                                                                   |
| Gelişmiş Seçenekler: arka uç Isteği   |                                   | Application Insights için *arka uç isteklerinin* günlüğe kaydedilip edilmeyeceğini belirtir. *Arka uç isteği* , Azure API Management hizmetinden giden bir istek.                                                                                                                                                                        |
| Gelişmiş Seçenekler: arka uç yanıtı  |                                   | Application Insights için *arka uç yanıtlarının* kaydedilip kaydedilmeyeceğini belirtir. *Arka uç yanıtı* , Azure API Management hizmetine gelen bir yanıttır.                                                                                                                                                                       |

> [!NOTE]
> Günlükçüleri farklı düzeylerde, tek API günlükçüsü veya tüm API 'Ler için bir günlükçü belirtebilirsiniz.
>  
> Her ikisini de belirtirseniz:
> + Bunlar farklı Günlükçüler ise, her ikisi de kullanılır (çoğullama günlükleri),
> + Bunlar aynı Günlükçüler, ancak farklı ayarlara sahip ise, tek bir API (daha ayrıntılı düzey) için bir tane, tüm API 'Leri için geçersiz kılınır.

## <a name="what-data-is-added-to-application-insights"></a>Application Insights eklenen veriler

Application Insights şunu alır:

+ Her gelen istek (*ön uç isteği*, *ön uç yanıtı*) için telemetri öğesi *iste* ,
+ Bir arka uç hizmetine iletilen her istek için *bağımlılık* telemetri öğesi (*arka uç isteği*, *arka uç yanıtı*),
+ Her başarısız istek için *özel durum* telemetrisi öğesi:
    + kapalı bir istemci bağlantısı nedeniyle başarısız oldu
    + API ilkelerinin *hata durumunda* tetiklenen bir bölümü tetiklendi
    + , 4xx veya 5xx ile eşleşen bir yanıt HTTP durum koduna sahiptir.
+ [İzleme](api-management-advanced-policies.md#Trace) ilkesini yapılandırırsanız, *izleme* telemetri öğesi. `severity`İlkedeki ayar, `trace` `verbosity` Application Insights günlüğe kaydetme ayarından daha büyük veya bu ayara eşit olmalıdır.

> [!NOTE]
> Application Insights örnek başına en büyük boyut ve ölçüm ve olay sayısı hakkında bilgi için bkz. [Application Insights sınırları](../azure-monitor/service-limits.md#application-insights) .

## <a name="performance-implications-and-log-sampling"></a>Performans etkileri ve günlük örnekleme

> [!WARNING]
> Tüm olayların günlüğe kaydedilmesi, gelen isteklerin hızına bağlı olarak ciddi performans etkilerine sahip olabilir.

İç yük testlerine bağlı olarak, bu özelliğin etkinleştirilmesi, istek hızı saniye başına 1.000 isteği aştığında üretilen iş için %40 %50 oranında azalmaya neden oldu. Application Insights, uygulama performanslarını değerlendirmek için istatistiksel analizler kullanmak üzere tasarlanmıştır. Bir denetim sistemi olması amaçlanmamıştır ve yüksek hacimli API 'Ler için her bireysel isteği günlüğe kaydetmeye uygun değildir.

**Örnekleme** ayarını ayarlayarak günlüğe kaydedilen isteklerin sayısını değiştirebilirsiniz (önceki adımlara bakın). %100 değeri, tüm isteklerin günlüğe kaydedildiği, %0 ' ın günlüğe kaydetme olmayacağı anlamına gelir. **Örnekleme** , önemli performans düşüşünü azaltmak, ancak günlüğe kaydetmenin avantajlarından yararlanmaya devam ederken telemetri hacmini azaltmaya yardımcı olur.

Üst bilgilerin ve istek gövdesinin ve yanıtların, hafifletmesini performans sorunları üzerinde olumlu bir etkisi olacaktır.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Application Insights](/azure/application-insights/)hakkında daha fazla bilgi edinin.
+ [Azure Event Hubs oturum açmayı](api-management-howto-log-event-hubs.md)göz önünde bulundurun.

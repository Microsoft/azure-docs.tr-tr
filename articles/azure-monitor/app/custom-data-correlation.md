---
title: Azure Application Insights | Microsoft Docs
description: ''
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 03f802297c2ecaac0c126ed3de6401b40e1f804b
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678173"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Özel veri kaynaklarıyla Application Insights verileri ilişkilendirme

Application Insights birkaç farklı veri türü toplar: özel durumlar, izlemeler, sayfa görünümleri ve diğerleri. Bu genellikle uygulamanızın performansını, güvenilirliğini ve kullanımını araştırmak için yeterli olmakla kalmaz, Application Insights ' de depolanan verileri diğer tamamen özel veri kümelerine ilişkilendirmek için faydalı olduğu durumlar vardır.

Özel verilerin şunları isteyebileceğiniz bazı durumlar:

- Veri zenginleştirme veya arama tabloları: Örneğin, sunucunun sahibine ve bulunduğu laboratuvar konumuna sahip bir sunucu adı verin 
- Application Insights olmayan veri kaynaklarıyla bağıntı: Örneğin, sevk etme süresi tahminlerinizin ne kadar doğru olduğunu anlamak için satın alma hizmeti 'ndeki bilgilerle bir web deposunda satın alma hakkındaki verileri ilişkilendirmek için 
- Tamamen özel veriler: pek çok müşterimiz, Application Insights yedekleyen Azure Izleyici günlük platformunun sorgu dilini ve performansını sevmez ve bu dosyayı, Application Insights ile ilişkili olmayan verileri sorgulamak için kullanmak ister. Örneğin, [burada](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)özetlenen bir akıllı giriş yüklemesinin parçası olarak güneş paneli performansını izlemek için.

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Application Insights verileriyle özel verileri ilişkilendirme 

Application Insights güçlü Azure Izleyici günlük platformu tarafından desteklendiğinden, verileri almak için Azure Izleyici 'nin tam gücünden yararlanabilirsiniz. Daha sonra, bu özel verileri Azure Izleyici günlüklerinde bize sunulan verilerle ilişkilendirileceği "JOIN" işlecini kullanarak sorgular yazacağız. 

## <a name="ingesting-data"></a>Veri giriş

Bu bölümde, verilerinizi Azure Izleyici günlüklerine nasıl alacağınız inceleniriz.

Henüz bir tane yoksa, [Bu yönergeleri](../learn/quick-collect-azurevm.md) izleyerek ve "bir çalışma alanı oluşturma" adımını dahil ederek yeni bir Log Analytics çalışma alanı sağlayın.

Günlük verilerini Azure Izleyici 'ye göndermeye başlamak için. Çeşitli seçenekler mevcuttur:

- Zaman uyumlu bir mekanizma için doğrudan [veri TOPLAYıCı API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) 'sini çağırabilir veya Logic App bağlayıcısını kullanabilirsiniz. "Azure Log Analytics" araması yapın ve "veri Gönder" seçeneğini belirlemeniz gerekir:

  ![Ekran görüntüsü seçme ve eylem](./media/custom-data-correlation/01-logic-app-connector.png)  

- Zaman uyumsuz bir seçenek için, veri toplayıcı API 'sini kullanarak bir işleme işlem hattı oluşturun. Ayrıntılar için [Bu makaleye](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) bakın.

## <a name="correlating-data"></a>Verileri ilişkilendirme

Application Insights, Azure Izleyici günlük platformunu temel alır. Bu nedenle, Application Insights verilerimizi kullanarak Azure Izleyici 'ye aldığımız tüm verileri ilişkilendirmek için [çapraz kaynak birleştirmeler](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) kullanabiliriz.

Örneğin, "myLA" adlı bir Log Analytics çalışma alanında laboratuvar envanterimizi ve konumlarımızı "LabLocations_CL" adlı bir tabloya alabiliriz. Daha sonra, "myAI" adlı Application Insights uygulamada izlenen isteklerimizi gözden geçirmek ve istekleri, daha önce bahsedilen özel tabloda depolanan bu makinelerin konumlarına karşılayan makine adlarını ilişkilendirmek isterse, aşağıdaki sorguyu şuradan çalıştırabiliriz Application Insights ya da Azure Izleyici bağlamı:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Sonraki Adımlar

- [Veri Toplayıcı API 'si](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) başvurusunu inceleyin.
- [Kaynak çapraz birleşimler](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)hakkında daha fazla bilgi için.

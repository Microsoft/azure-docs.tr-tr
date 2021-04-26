---
title: Azure Application Insights | Microsoft Docs
description: Veri zenginleştirme veya arama tabloları, Application Insights olmayan veri kaynakları ve özel veriler gibi Application Insights verileri diğer veri kümelerine bağıntılı.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 933280b5d3b81098f18f22a72bd2c7f942869e6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578331"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Özel veri kaynaklarıyla Application Insights verileri ilişkilendirme

Application Insights birkaç farklı veri türü toplar: özel durumlar, izlemeler, sayfa görünümleri ve diğerleri. Bu genellikle uygulamanızın performansını, güvenilirliğini ve kullanımını araştırmak için yeterli olmakla kalmaz, Application Insights ' de depolanan verileri diğer tamamen özel veri kümelerine ilişkilendirmek için faydalı olduğu durumlar vardır.

Özel verilerin şunları isteyebileceğiniz bazı durumlar:

- Veri zenginleştirme veya arama tabloları: Örneğin, sunucunun sahibine ve bulunduğu laboratuvar konumuna sahip bir sunucu adı verin 
- Application Insights olmayan veri kaynaklarıyla bağıntı: Örneğin, sevk etme süresi tahminlerinizin ne kadar doğru olduğunu anlamak için satın alma hizmeti 'ndeki bilgilerle bir web deposunda satın alma hakkındaki verileri ilişkilendirmek için 
- Tamamen özel veriler: pek çok müşterimiz, Application Insights yedekleyen Azure Izleyici günlük platformunun sorgu dilini ve performansını sevmez ve bu dosyayı, Application Insights ile ilişkili olmayan verileri sorgulamak için kullanmak ister. Örneğin, [burada](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)özetlenen bir akıllı giriş yüklemesinin parçası olarak güneş paneli performansını izlemek için.

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Application Insights verileriyle özel verileri ilişkilendirme 

Application Insights güçlü Azure Izleyici günlük platformu tarafından desteklendiğinden, verileri almak için Azure Izleyici 'nin tam gücünden yararlanabilirsiniz. Daha sonra, bu özel verileri Azure Izleyici günlüklerinde bize sunulan verilerle ilişkilendirileceği "JOIN" işlecini kullanarak sorgular yazacağız. 

## <a name="ingesting-data"></a>Verileri alma

Bu bölümde, verilerinizi Azure Izleyici günlüklerine nasıl alacağınız inceleniriz.

Henüz bir tane yoksa, [Bu yönergeleri](../vm/quick-collect-azurevm.md) izleyerek ve "bir çalışma alanı oluşturma" adımını dahil ederek yeni bir Log Analytics çalışma alanı sağlayın.

Günlük verilerini Azure Izleyici 'ye göndermeye başlamak için. Çeşitli seçenekler mevcuttur:

- Zaman uyumlu bir mekanizma için doğrudan [veri TOPLAYıCı API](../logs/data-collector-api.md) 'sini çağırabilir veya Logic App bağlayıcısını kullanabilirsiniz. "Azure Log Analytics" araması yapın ve "veri Gönder" seçeneğini belirlemeniz gerekir:

  ![Ekran görüntüsü seçme ve eylem](./media/custom-data-correlation/01-logic-app-connector.png)  

- Zaman uyumsuz bir seçenek için, veri toplayıcı API 'sini kullanarak bir işleme işlem hattı oluşturun. Ayrıntılar için [Bu makaleye](../logs/create-pipeline-datacollector-api.md) bakın.

## <a name="correlating-data"></a>Verileri ilişkilendirme

Application Insights, Azure Izleyici günlük platformunu temel alır. Bu nedenle, Application Insights verilerimizi kullanarak Azure Izleyici 'ye aldığımız tüm verileri ilişkilendirmek için [çapraz kaynak birleştirmeler](../logs/cross-workspace-query.md) kullanabiliriz.

Örneğin, "myLA" adlı bir Log Analytics çalışma alanında laboratuvar envanterimizi ve konumlarımızı "LabLocations_CL" adlı bir tabloya alabiliriz. Bundan sonra, "myAI" adlı Application Insights uygulamada izlenen isteklerimizi gözden geçirmek ve istekleri, daha önce bahsedilen özel tabloda depolanan bu makinelerin konumlarına karşılayan makine adlarını ilişkilendirmek isterse, Application Insights veya Azure Izleyici bağlamından aşağıdaki sorguyu çalıştırabiliriz:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Sonraki Adımlar

- [Veri Toplayıcı API 'si](../logs/data-collector-api.md) başvurusunu inceleyin.
- [Kaynak çapraz birleşimler](../logs/cross-workspace-query.md)hakkında daha fazla bilgi için.

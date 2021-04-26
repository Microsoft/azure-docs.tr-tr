---
title: Azure API Management sorunları tanılayın ve çözün
description: Azure portal tanılama ve çözme aracıyla Azure API Management API 'niz ile ilgili sorunları nasıl giderebileceğinizi öğrenin.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652411"
---
# <a name="azure-api-management-diagnostics-overview"></a>Azure API Management tanılamalarına genel bakış

Azure API Management 'de bir API oluşturup yönetbaşladığınızda, 404 ' den hatalı ağ geçidi hatası 502 olarak ' den kaynaklanan sorunlar için hazırlıklı olmak istersiniz. API Management tanılama, APıM 'de yayınlanan API 'nizin yapılandırması gerekmeden sorun gidermenize yardımcı olan akıllı ve etkileşimli bir deneyimdir. Yayımlanan API 'lerinizle ilgili sorunlar yaşıyorsanız API Management tanılama, nelerin yanlış olduğunu gösterir ve sorunu hızla gidermek ve çözmek için sizi doğru bilgilere yönlendirir.

Son 24 saat içinde API 'niz ile ilgili sorun yaşadığınızda bu deneyim en çok yararlı olsa da, tüm tanılama grafikleri her zaman analiz etmeniz için kullanılabilir.

## <a name="open-api-management-diagnostics"></a>API Management tanılamayı aç

API Management tanılama 'ya erişmek için [Azure portal](https://portal.azure.com)API Management hizmet örneğinize gidin. Sol gezinti bölmesinde **Tanıla ve sorunları çöz**' ü seçin.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="Ekran görüntüsünde, tanılama 'ya nasıl gidebileceğiniz gösterilmektedir.":::



## <a name="intelligent-search"></a>Akıllı arama

Sorunlarınızı veya sorunlarınızı sayfanın en üstündeki arama çubuğunda arayabilirsiniz. Arama Ayrıca sorunlarınızı gidermenize veya çözmenize yardımcı olabilecek araçları bulmanıza de yardımcı olur. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="Akıllı arama 'nın ekran görüntüsü.":::


## <a name="troubleshooting-categories"></a>Sorun giderme kategorileri

Kategoriler altındaki sorunları giderebilirsiniz. API performanize, ağ geçidinize, API ilkelerinize ve hizmet katmanıyla ilgili yaygın sorunlar, her kategori içinde analiz edilebilir. Her kategori Ayrıca daha belirli tanılama denetimleri de sağlar. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="Kategoriye genel bakış ekran görüntüsü.":::


### <a name="availability-and-performance"></a>Kullanılabilirlik ve performans

Bu kategori altındaki API kullanılabilirliği ve performans sorunlarınızı denetleyin. Bu kategori kutucuğunu seçtikten sonra, bir etkileşimli arabirimde birkaç ortak denetim önerildiğini göreceksiniz. Her bir sorunun ayrıntılarını incelemek için her bir denetimi tıklatın. Bu denetim, API performansınızı ve performans sorunlarının bir özetini gösteren bir grafik de sağlar. Örneğin, API hizmetiniz arka uçta son saat içinde 5xx hatası ve zaman aşımı olmuş olabilir. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Etkileşimli arabirim denetiminin ekran görüntüsü 1.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Etkileşimli arabirim denetiminin ekran görüntüsü 2.":::

### <a name="api-policies"></a>API ilkeleri

Bu kategori hataları algılar ve ilke sorunlarınızı size bildirir. 

Benzer bir etkileşimli arabirim, API ilkeleri yapılandırmanızın sorunlarını gidermenize yardımcı olması için size veri ölçümlerine kılavuzluk eder.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="API Ilkeleri kategori kutucuğunun ekran görüntüsü.":::

### <a name="gateway-performance"></a>Ağ geçidi performansı 

Ağ Geçidi istekleri veya yanıtları ya da ağ geçidinizdeki 4xx veya 5xx hatası için bu kategoriyi izlemek ve sorunlarını gidermek için kullanın. Benzer şekilde, API Gateway performansınızı denetlemek istediğiniz belirli bir alana göz atın ve etkileşimli arabiriminden yararlanın. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="Ağ Geçidi Performans kategorisi kutucuğunun ekran görüntüsü.":::

### <a name="service-upgrade"></a>Hizmet yükseltmesi

Bu kategori, şu anda kullanmakta olduğunuz hizmet katmanını (SKU) denetler ve bu katmanla ilgili olabilecek sorunlardan kaçınmak için yükseltmeniz hatırlatır. Aynı etkileşimli arabirim, daha fazla grafik ve Özet denetim sonucuyla daha derin bir şekilde gitmenize yardımcı olur. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="hizmet yükseltme kategorisi kutucuğunun ekran görüntüsü.":::

## <a name="search-documentation"></a>Belgelerde arayın

Tanılama ve çözme sorunları araçlarına ek olarak, sorununuzla ilgili sorun giderme belgelerini de arayabilirsiniz. Tanılama 'yı hizmetinizde çalıştırdıktan sonra etkileşimli arabirimdeki **belgeleri ara** ' yı seçin. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="Arama belgeleri işlevinin nasıl kullanılacağını gösteren ekran görüntüsü 1.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="Arama belgelerinin nasıl kullanılacağına ilişkin 2 ekran görüntüsü.":::


## <a name="next-steps"></a>Sonraki adımlar

* [API](howto-use-analytics.md) analizini, API 'lerin kullanımını ve performansını analiz etmek için de kullanın. 
* Tanılama sorunlarını Web Apps gidermek mi istiyorsunuz? [Buradan](../app-service/overview-diagnostics.md) okuyun
* Azure Kubernetes hizmetleri sorunlarını denetlemek için tanılamalara yararlanın. [AKS 'Teki tanılamayı](../aks/concepts-diagnostics.md) inceleyin
* Başlığa "[diag]" ekleyerek sorularınızı veya görüşlerinizi [UserVoice](https://feedback.azure.com/forums/248703-api-management) 'a gönderin.

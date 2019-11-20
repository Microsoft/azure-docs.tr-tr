---
title: Azure API Management örneğinin kapasitesi | Microsoft Docs
description: Bu makalede, kapasite ölçüsünün ne olduğu ve Azure API Management örneğinin ölçeklendirilmesine bakılmaksızın bilinçli kararlar alma açıklanmaktadır.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: a585ab059319b15be1f2a86bf10b7dc58da72494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299457"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Azure API Management örneğinin kapasitesi

**Kapasite** , bir API Management örneğini daha fazla yüklemeye uyum sağlayacak şekilde ölçeklendirmenize bakılmaksızın bilinçli kararlar almak için en önemli [Azure izleyici ölçümdür](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) . Oluşturma karmaşıktır ve belirli davranışları uygular.

Bu makalede **kapasitenin** ne olduğu ve nasıl davrandığı açıklanmaktadır. Azure portal **Kapasite** ölçümlerinde nasıl erişebileceğiniz ve API Management örneğinizi ölçeklendirmenin veya yükseltmenin ne zaman yapılacağını öneren gösterilmektedir.

> [!IMPORTANT]
> Bu makalede, Azure API Management örneğinizi kapasite ölçüsüne göre izleyip ölçeklendirebileceğinizi ele alınmaktadır. Ancak, tek bir API Management örneği kapasiteye *ulaştığı* zaman ne olduğunu anlamak da aynı şekilde önemlidir. Azure API Management örneklerin fiziksel olarak aşırı yüklenmesine engel olmak için herhangi bir hizmet düzeyi azaltmayı uygulamacaktır. Bir örnek fiziksel kapasitesine ulaştığında, gelen istekleri işleyemeyecek aşırı yüklenmiş web sunucusuna benzer şekilde davranır: gecikme artar, bağlantılar bırakılır, zaman aşımı hataları oluşur, vb. olur. Bu, API istemcilerinin diğer bir dış hizmetle (örn. yeniden deneme ilkeleri uygulanarak) olduğu gibi bu olasılığa yönelik hazırlanmak üzere hazırlanması gerektiği anlamına gelir.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları takip etmek için şunları yapmanız gerekir:

+ Etkin bir Azure aboneliği.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Bir APıM örneği. Daha fazla bilgi için bkz. [Azure API Management örneği oluşturma](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Kapasite nedir?

![Kapasite ölçümü](./media/api-management-capacity/capacity-ingredients.png)

**Kapasite** , bir API Management örneğindeki yükün göstergesidir. Kaynak kullanımını (CPU, bellek) ve ağ kuyruğu uzunluklarını yansıtır. CPU ve bellek kullanımı, kaynakların tüketimini şu şekilde gösterir:

+ İstekleri iletme veya bir ilke çalıştırma içerebilen istek işleme gibi veri düzlemi hizmetlerini API Management.
+ Azure portalı veya ARM aracılığıyla uygulanan yönetim eylemleri veya [Geliştirici portalından](api-management-howto-developer-portal.md)gelen yükleme gibi yönetim düzlemi Hizmetleri API Management.
+ Yeni bağlantılarda SSL el sıkışmaları içeren süreçler dahil olmak üzere seçili işletim sistemi işlemi.

Toplam **Kapasite** , bir API Management örneğinin her biriminden kendi değerlerinin ortalamasıdır.

**Kapasite ölçümü** API Management örneğinizle ilgili sorunları yüzeye sunacak şekilde tasarlansa da, sorunların **Kapasite ölçümünde**değişikliklere yansıtılmayabileceği durumlar vardır.

## <a name="capacity-metric-behavior"></a>Kapasite ölçümü davranışı

Yapısı nedeniyle gerçek yaşam **kapasitesi** birçok değişken tarafından etkilenebilir, örneğin:

+ bağlantı desenleri (bir istek üzerindeki yeni bağlantı, var olan bağlantıyı yeniden kullanmaya karşı)
+ istek ve yanıt boyutu
+ her API veya istek gönderen istemci sayısı üzerinde yapılandırılan ilkeler.

İsteklerde daha karmaşık işlemler olduğunda **Kapasite** tüketiminin daha yüksek olması gerekir. Örneğin, karmaşık dönüştürme ilkeleri basit bir istek iletmeden çok daha fazla CPU kullanır. Yavaş arka uç hizmeti yanıtları de artacaktır.

> [!IMPORTANT]
> **Kapasite** , işlenen istek sayısının doğrudan ölçüsü değildir.

![Kapasite ölçümü ani artışlar](./media/api-management-capacity/capacity-spikes.png)

Ayrıca, işlenen bir istek olmasa bile **Kapasite** aralıklı olarak veya sıfırdan büyük olabilir. Sistem veya platforma özel eylemler nedeniyle oluşur ve bir örneği ölçeklendirmeye karar verirken dikkate alınmamalıdır.

Düşük **kapasiteli ölçüm** , API Management örneğinizin herhangi bir sorun yaşamaması anlamına gelmez.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Kapasiteyi incelemek için Azure portalını kullanma
  
![Kapasite ölçümü](./media/api-management-capacity/capacity-metric.png)  

1. [Azure Portal](https://portal.azure.com/)APIM örneğinize gidin.
2. **Ölçümler**’i seçin.
3. Mor bölümünde, kullanılabilir ölçülerden **Kapasite** ölçümü ' ni seçin ve varsayılan **Ort** toplamayı bırakın.

    > [!TIP]
    > Yanlış yorumlamalar yapmaktan kaçınmak için her zaman konum başına **Kapasite** ölçüm dökümüne bakmanız gerekir.

4. Yeşil bölümden ölçüyü boyuta göre bölmek için **konum** ' u seçin.
5. Bölümün üst çubuğundan istediğiniz zaman çerçevesini seçin.

    Bir ölçüm uyarısını, ne zaman beklenmeyen bir şey olduğunu bildirmek için ayarlayabilirsiniz. Örneğin, APıM örneğiniz 20 dakikadan fazla süre için beklenen en yüksek kapasiteyi aşmışsa bildirim alın.

    >[!TIP]
    > Azure API Management birimini otomatik olarak eklemek için hizmetinizin kapasiteye göre düşük veya Azure Izleyici otomatik ölçeklendirme işlevini kullanma hakkında bilgi almak için uyarıları yapılandırabilirsiniz. Ölçeklendirme işlemi 30 dakika sürebilir, bu nedenle kurallarınızı uygun şekilde planlamanız gerekir.  
    > Yalnızca ana konumun ölçeklendirilmesine izin verilir.

## <a name="use-capacity-for-scaling-decisions"></a>Kararları ölçeklendirmek için kapasiteyi kullanın

**Kapasite** , bir API Management örneğini daha fazla yüklemeye uyum sağlayacak şekilde ölçeklendirmeye yönelik kararlar alma ölçümdür. Göz önünde bulundurun:

+ Uzun süreli eğilim ve ortalamaya bakıyor.
+ Yükte hiçbir artışla ilgisi olmayan ani artışlar yok sayılıyor (bkz. "kapasite ölçüm davranışı" Açıklama).
+ **Kapasitenin**değeri, daha uzun bir süre boyunca% 60 veya% 70 ' i aştığında, örneğinizi yükseltmek veya ölçeklendirmek (örneğin, 30 dakika). Farklı değerler, hizmetiniz veya senaryonuz için daha iyi çalışabilir.

>[!TIP]  
> Trafiğinizi önceden tahmin edebiliyorsanız, istediğiniz iş yükleri için APıM örneğinizi test edin. Kiracınızdaki istek yükünü kademeli olarak artırabilir ve kapasite ölçümünün en yüksek yüküne karşılık gelen değerini izleyebilirsiniz. Herhangi bir zamanda ne kadar kapasite kullanıldığını anlamak için Azure portal kullanmak için önceki bölümde verilen adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure API Management hizmet örneğini ölçeklendirme veya yükseltme](upgrade-and-scale.md)
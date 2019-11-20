---
title: Azure Izleyici 'den verileri görselleştirme | Microsoft Docs
description: Azure Izleyici 'de depolanan ölçüm ve günlük verilerini görselleştirmek için kullanılabilen yöntemlerin bir özetini sağlar.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/24/2018
ms.openlocfilehash: c350cb1f8e7343f6d8e7a416aa0bba71388eaa86
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597828"
---
# <a name="visualizing-data-from-azure-monitor"></a>Azure Izleyici 'den verileri görselleştirme
Bu makalede, Azure Izleyici 'de depolanan günlük ve ölçüm verilerini görselleştirmek için kullanılabilen yöntemlerin bir özeti sağlanmaktadır.

Grafikler ve grafikler gibi görselleştirmeler, sorunların ayrıntılarına inmek ve desenleri belirlemek için izleme verilerinizi çözümlemenize yardımcı olabilir. Kullandığınız araca bağlı olarak, görselleştirmeleri kuruluşunuzun içindeki ve dışındaki diğer kullanıcılarla paylaşma seçeneğiniz de olabilir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-dashboards"></a>Azure panoları
[Azure panoları](../azure-portal/azure-portal-dashboards.md) , Azure için birincil kesik taslak teknolojisidir. Bunlar özellikle, önemli sorunları hızlı bir şekilde tanımanıza olanak tanıyacak şekilde, Azure altyapınız ve hizmetleriniz üzerinde tek bir cam bölmesi sağlamak için yararlıdır.

![Pano](media/visualizations/dashboard.png)

### <a name="advantages"></a>Üstünlü
- Azure ile derin tümleştirme. Görselleştirmeler, Ölçüm Gezgini, Log Analytics ve Application Insights dahil olmak üzere birden çok Azure sayfasında bulunan panolara sabitlenebilir.
- Hem ölçümleri hem de günlükleri destekler.
- [Ölçüm Gezgini](platform/metrics-charts.md), [günlük sorguları](log-query/log-query-overview.md)ve Application Insights ' deki [haritalar](app/app-map.md) ve kullanılabilirliğin çıktısı dahil olmak üzere birden fazla kaynaktaki verileri birleştirin.
- Kişisel veya paylaşılan panolar için seçeneği. Azure [rol tabanlı kimlik doğrulaması (RBAC)](../role-based-access-control/overview.md)ile tümleşiktir.
- Otomatik yenileme. Ölçüm yenileme, en az beş dakikalık zaman aralığına bağlıdır. Belirli bir görselleştirmede "Yenile" simgesine tıklayarak veya tam panoyu yenileyerek isteğe bağlı el ile yenileme seçeneği ile her saat yenileme günlüğü.
- Zaman damgası ve özel parametrelerle Parametrized ölçüm panoları.
- Esnek düzen seçenekleri.
- Tam ekran modu.


### <a name="limitations"></a>Sınırlamalar
- Veri tabloları desteği olmayan günlük görselleştirmeler üzerinde sınırlı denetim. Toplam veri serisi sayısı, _diğer_ bir demet altında gruplanmış daha fazla veri serisi ile 10 ile sınırlıdır.
- Günlük grafikleri için özel parametre desteği yok.
- Günlük grafikleri son 30 güne sınırlıdır.
- Günlük grafikleri yalnızca paylaşılan panolara sabitlenebilir.
- Pano verileriyle etkileşim yoktur.
- Sınırlı bağlamsal ayrıntıya gitme.

## <a name="azure-monitor-views"></a>Azure Izleyici görünümleri
[Azure izleyici 'de görünümler](platform/view-designer.md) , günlük verileriyle özel görselleştirmeler oluşturmanıza olanak sağlar. Bunlar, topladıkları verileri sunmak için [çözümler izlenerek](insights/solutions.md) kullanılır.

![Görüntüle](media/visualizations/view.png)

### <a name="advantages"></a>Üstünlü
- Günlük verileri için zengin görselleştirmeler.
- Diğer kaynak gruplarına ve aboneliklerine aktarmak için görünümleri dışa ve içeri aktarma.
- Çalışma alanları ve izleme çözümleri ile Azure Izleyici yönetim modeli ile tümleşir.
- Özel parametrelere yönelik [Filtreler](platform/view-designer-filters.md) .
- Etkileşimli, çok düzeyli detaya gitmeyi destekler (başka bir görünümde ayrıntıya gider görünümü)

### <a name="limitations"></a>Sınırlamalar
- , Günlükleri destekler ancak ölçümleri destekler.
- Kişisel görünüm yok. Çalışma alanına erişimi olan tüm kullanıcılar tarafından kullanılabilir.
- Otomatik yenileme yok.
- Sınırlı düzen seçenekleri.
- Birden çok çalışma alanı üzerinde veya Application Insights uygulamada sorgulama desteği yoktur.
- Sorgular, yanıt boyutu ile 8MB ve sorgu yürütme süresi olarak 110 saniye sınırlıdır.


## <a name="workbooks"></a>Çalışma Kitapları
[Çalışma kitapları](../azure-monitor/app/usage-workbooks.md) , ekip içinde verileriniz, araştırılması ve işbirliği hakkında ayrıntılı Öngörüler sağlayan etkileşimli belgelerdir. Çalışma kitaplarının yararlı olduğu belirli örnekler, sorun giderme kılavuzlarından ve olay postmordıtem.

![Kitabının](media/visualizations/workbook.png)

### <a name="advantages"></a>Üstünlü
- Hem ölçümleri hem de günlükleri destekler.
- , Tablodaki bir öğenin seçilmesi ilişkili grafikleri ve görselleştirmeleri dinamik olarak güncelleştirecek etkileşimli raporları etkinleştiren parametreleri destekler.
- Belge benzeri akış.
- Kişisel veya paylaşılan çalışma kitapları için seçenek.
- Kolay, işbirliğiyle kolay yazma deneyimi.
- Şablonlar, genel GitHub tabanlı şablon galerisini destekler.

### <a name="limitations"></a>Sınırlamalar
- Otomatik yenileme yok.
- Panolar gibi yoğun bir düzen, çalışma kitaplarını tek bir cam bölmesi olarak daha az faydalı hale getirir. Daha derin Öngörüler sağlamaya yönelik daha fazla bilgi edinin.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) , özellikle de uzun süreli KPI eğilimlerini çözümleyen raporlar ve iş merkezli panolar ve raporlar oluşturmak için kullanışlıdır. [Bir günlük sorgusunun sonuçlarını](platform/powerbi.md) bir Power BI veri kümesine aktarabilirsiniz, böylece farklı kaynaklardaki verileri birleştirme ve Web ve mobil cihazlarda rapor paylaşma gibi özelliklerden yararlanabilirsiniz.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Üstünlü
- Zengin görselleştirmeler.
- Yakınlaştırma ve çapraz filtreleme dahil olmak üzere kapsamlı etkileşim.
- Kuruluşunuz genelinde kolayca paylaşabilirsiniz.
- Birden çok veri kaynağından diğer verilerle tümleştirme.
- Bir küpte önbelleğe alınmış sonuçlarla daha iyi performans.


### <a name="limitations"></a>Sınırlamalar
- , Günlükleri destekler ancak ölçümleri destekler.
- Azure tümleştirmesi yok. Azure Resource Manager aracılığıyla panoları ve modelleri yönetemezsiniz.
- Sorgu sonuçlarının yapılandırmak için Power BI modele aktarılması gerekir. Sonuç boyutu ve yenileme sınırlaması.
- Günde sekiz kez sınırlı veri yenileme.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) , işlemsel panolar içinde bulunan açık bir platformdur. İşlem olaylarını saptamak ve yalıtmak ve önceliklendirme için özellikle yararlıdır. Azure ölçüm verilerinizi görselleştirmesini sağlamak için Azure aboneliğinize [Grafana Azure izleyici veri kaynağı eklentisi](platform/grafana-plugin.md) ekleyebilirsiniz.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Üstünlü
- Zengin görselleştirmeler.
- Veri kaynaklarının zengin ekosistemi.
- Yakınlaştırma dahil veri etkileşimi.
- Parametreleri destekler.

### <a name="limitations"></a>Sınırlamalar
- Azure tümleştirmesi yok. Azure Resource Manager aracılığıyla panoları ve modelleri yönetemezsiniz.
- Ek Grafana altyapısını destekleme maliyeti veya Grafana bulutu için ek maliyet.


## <a name="build-your-own-custom-application"></a>Kendi özel uygulamanızı oluşturun
Azure Izleyici 'deki günlük ve ölçüm verilerinde bulunan verilere, kendi özel web sitelerinizi ve uygulamalarınızı oluşturmanıza olanak sağlayan herhangi bir REST istemcisi kullanarak API 'leri aracılığıyla erişebilirsiniz.

### <a name="advantages"></a>Üstünlü
- Kullanıcı arabirimi, görselleştirme, etkileşim ve özelliklerde esnekliği tamamen yapın.
- Ölçümleri ve günlük verilerini diğer veri kaynaklarıyla birleştirin.

### <a name="disadvantages"></a>Olumsuz
- Önemli mühendislik çabaları gerekiyor.


## <a name="next-steps"></a>Sonraki adımlar
- [Azure izleyici tarafından toplanan veriler](platform/data-platform.md)hakkında bilgi edinin.
- [Azure panoları](../azure-portal/azure-portal-dashboards.md)hakkında bilgi edinin.
- [Azure izleyici 'de görünümler](platform/view-designer.md)hakkında bilgi edinin.
- [Çalışma kitapları](../azure-monitor/app/usage-workbooks.md)hakkında bilgi edinin.
- [Günlük verilerini Power BI içeri aktarma](../azure-monitor/platform/powerbi.md)hakkında bilgi edinin.
- [Grafana Azure izleyici veri kaynağı eklentisi](../azure-monitor/platform/grafana-plugin.md)hakkında bilgi edinin.


---
title: Uygulamaları izleme-Azure App Service | Microsoft Docs
description: Azure portal kullanarak Azure App Service uygulamaları nasıl izleyeceğinizi öğrenin.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 1cfab9b065fd4e28a9ce11ac85682a298011200b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470130"
---
# <a name="monitor-apps-in-azure-app-service"></a>Azure App Service uygulamaları izleme
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) , [Azure Portal](https://portal.azure.com)Web Apps, MOBIL arka uçlar ve API uygulamaları için yerleşik izleme işlevleri sağlar.

Azure portal, bir uygulama için *kotaları* ve *ölçümleri* gözden geçirebilir, App Service planını inceleyebilir ve ölçümleri temel alan *uyarıları* ve *ölçeklendirmeyi* otomatik olarak ayarlayabilirsiniz.

## <a name="understand-quotas"></a>Kotaları anlama

App Service barındırılan uygulamalar, kullanabilecekleri kaynaklar üzerinde belirli sınırlara tabidir. Sınırlar, uygulamayla ilişkili App Service planı tarafından tanımlanır.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Uygulama *ücretsiz* veya *paylaşılan* bir planda barındırılıyorsa, uygulamanın kullanabileceği kaynakların sınırları Kotalar tarafından tanımlanır.

Uygulama *temel*, *Standart*veya *Premium* bir planda barındırılıyorsa, kullandıkları kaynaklarla ilgili sınırlar, App Service planındaki *Boyut* (küçük, orta, büyük) ve *örnek sayısı* (1, 2, 3, vb.) ile ayarlanır.

Ücretsiz veya paylaşılan uygulamalar için kotalar şunlardır:

| Kota | Açıklama |
| --- | --- |
| **CPU (kısa)** | Bu uygulama için, 5 dakikalık bir aralıkta izin verilen CPU miktarı. Bu kota her beş dakikada bir sıfırlanır. |
| **CPU (gün)** | Bu uygulama için bir günde izin verilen toplam CPU miktarı. Bu kota, her 24 saatte bir, gece yarısı UTC 'yi sıfırlar. |
| **Bellek** | Bu uygulama için izin verilen toplam bellek miktarı. |
| **Bant Genişliği** | Bu uygulama için günde izin verilen giden bant genişliğinin toplam miktarı. Bu kota, her 24 saatte bir, gece yarısı UTC 'yi sıfırlar. |
| **Biçimlendiri** | İzin verilen toplam depolama miktarı. |

*Temel*, *Standart*ve *Premium* planlarında barındırılan uygulamalar Için geçerli olan tek Kota dosya sistemi 'dir.

Çeşitli App Service SKU 'Larının kullanabildiği belirli kotalar, sınırlar ve özellikler hakkında daha fazla bilgi için bkz. [Azure abonelik hizmeti sınırları](../azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Kota zorlaması

Bir uygulama *CPU (Short)* , *CPU (gün)* veya *bant genişliği* kotasını aşarsa, kota sıfırlanana kadar uygulama durdurulur. Bu süre boyunca, tüm gelen istekler HTTP 403 hatasına neden olacak.

![403 hata iletisi][http403]

Uygulama belleği kotası aşılırsa, uygulama yeniden başlatılır.

Dosya sistemi kotası aşılırsa, herhangi bir yazma işlemi başarısız olur. Yazma işlemi hatalarında günlüklere yazma işlemleri dahildir.

App Service planınızı yükselterek, kotalarınızı uygulamanızı artırabilir veya kaldırabilirsiniz.

## <a name="understand-metrics"></a>Ölçümleri anlama

Ölçümler, uygulama veya App Service planının davranışı hakkında bilgi sağlar.

Bir uygulama için kullanılabilir ölçümler şunlardır:

| Ölçüm | Açıklama |
| --- | --- |
| **Ortalama yanıt süresi** | Uygulama için geçen ortalama süre (saniye cinsinden). |
| **Ortalama bellek çalışma kümesi** | Uygulama tarafından, megabayt (MIB) cinsinden kullanılan ortalama bellek miktarı. |
| **Bağlantılar** | Korumalı alan içindeki ilişkili yuva sayısı (W3wp. exe ve onun alt işlemi).  Bağlama yuvası, bind ()/Connect () API 'Leri çağırarak ve bir yuva CloseHandle ()/Closesocket () ile kapatılana kadar kalır. |
| **CPU süresi** | Uygulama tarafından saniye cinsinden tüketilen CPU miktarı. Bu ölçüm hakkında daha fazla bilgi için bkz. [CPU saati vs CPU yüzdesi](#cpu-time-vs-cpu-percentage). |
| **Geçerli derlemeler** | Bu uygulamadaki tüm AppDomain 'ler genelinde yüklenen derlemelerin geçerli sayısı. |
| **Içindeki veriler** | MIB içinde uygulama tarafından tüketilen gelen bant genişliği miktarı. |
| **Giden veriler** | MIB içinde uygulama tarafından tüketilen giden bant genişliği miktarı. |
| **Gen 0 çöp koleksiyonları** | Uygulama işleminin başlangıcından bu yana nesil 0 nesnelerinin atık olarak toplandığı sayı. Daha yüksek nesil GCs, tüm düşük nesil GCs 'leri içerir.|
| **Gen 1 çöp koleksiyonları** | Uygulama işleminin başlangıcından bu yana 1. nesil nesnelerin atık olarak toplandığı sayı. Daha yüksek nesil GCs, tüm düşük nesil GCs 'leri içerir.|
| **Gen 2 çöp koleksiyonları** | Oluşturma 2 nesnelerinin, uygulama işleminin başlangıcından bu yana atık olarak toplandığı sayı.|
| **Tanıtıcı sayısı** | Uygulama işlemi tarafından şu anda açık olan tanıtıcı sayısının toplam sayısı.|
| **Http 2xx** | HTTP durum kodu ≥ 200 ile sonuçlanan isteklerin sayısı, ancak 300 <. |
| **Http 3xx** | HTTP durum kodu ≥ 300 ile sonuçlanan isteklerin sayısı, ancak 400 <. |
| **Http 401** | HTTP 401 durum koduna neden olan istek sayısı. |
| **Http 403** | HTTP 403 durum koduna neden olan istek sayısı. |
| **HTTP 404** | HTTP 404 durum koduna neden olan istek sayısı. |
| **Http 406** | HTTP 406 durum koduna neden olan istek sayısı. |
| **Http 4xx** | HTTP durum kodu ≥ 400 ile sonuçlanan isteklerin sayısı, ancak 500 <. |
| **Http sunucu hataları** | HTTP durum kodu ≥ 500 ile sonuçlanan isteklerin sayısı, ancak 600 <. |
| **GÇ diğer bayt/saniye** | Uygulama işleminin, denetim işlemleri gibi verileri içermeyen g/ç işlemlerine bayt verme hızıdır.|
| **GÇ diğer Işlem/saniye** | Uygulama işleminin okuma veya yazma işlemleri olmayan g/ç işlemlerini verme oranı.|
| **GÇ okuma bayt/saniye** | Uygulama işleminin g/ç işlemlerinden bayt okuma hızıdır.|
| **GÇ okuma Işlemi/saniye** | Uygulama işleminin okuma g/ç işlemlerini verme hızıdır.|
| **GÇ yazma bayt/saniye** | Uygulama işleminin g/ç işlemlerine bayt yazma hızıdır.|
| **GÇ Yazma Işlemi/saniye** | Uygulama işleminin yazma g/ç işlemlerini verme hızıdır.|
| **Bellek çalışma kümesi** | Uygulama tarafından, MIB 'de kullanılan geçerli bellek miktarı. |
| **Özel baytlar** | Özel baytlar, uygulama işleminin ayırdığı ve diğer işlemlerle paylaşılamayan belleğin bayt cinsinden geçerli boyutudur.|
| **İstekleri** | Elde edilen HTTP durum kodundan bağımsız olarak isteklerin toplam sayısı. |
| **Uygulama kuyruğundaki istekler** | Uygulama istek sırasındaki isteklerin sayısı.|
| **İş parçacığı sayısı** | Uygulama işleminde Şu anda etkin olan iş parçacıklarının sayısı.|
| **Toplam uygulama etki alanları** | Bu uygulamada yüklü olan AppDomain 'in geçerli sayısı.|
| **Toplam yüklenmeyen uygulama etki alanları** | Uygulamanın başlangıcından bu yana bellekten kaldırılan toplam AppDomain sayısı.|


App Service planı için kullanılabilir ölçümler şunlardır:

> [!NOTE]
> App Service planı ölçümleri yalnızca *temel*, *Standart*ve *Premium* katmanlardaki planlar için kullanılabilir.
> 

| Ölçüm | Açıklama |
| --- | --- |
| **CPU yüzdesi** | Planın tüm örnekleri genelinde kullanılan ortalama CPU. |
| **Bellek yüzdesi** | Planın tüm örnekleri genelinde kullanılan ortalama bellek. |
| **Içindeki veriler** | Planın tüm örnekleri genelinde kullanılan ortalama gelen bant genişliği. |
| **Giden veriler** | Planın tüm örneklerinde kullanılan ortalama giden bant genişliği. |
| **Disk kuyruğu uzunluğu** | Depolama üzerinde sıraya alınan okuma ve yazma isteklerinin ortalama sayısı. Yüksek disk kuyruğu uzunluğu aşırı disk g/ç nedeniyle yavaşlabilen bir uygulamanın göstergesidir. |
| **Http kuyruğu uzunluğu** | Yerine getirilmeden önce kuyruğa oturmak zorunda olan HTTP isteklerinin ortalama sayısı. Yüksek veya artan bir HTTP kuyruğu uzunluğu, ağır yük altında bir planın belirtisidir. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU süresi vs CPU yüzdesi
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

CPU kullanımını yansıtan iki ölçüm vardır:

**CPU süresi**: ücretsiz veya paylaşılan planlarda barındırılan uygulamalar için yararlıdır, çünkü kotalarından biri uygulama tarafından kullanılan CPU dakikalarında tanımlanmıştır.

**CPU yüzdesi**: temel, standart ve Premium planlarında barındırılan uygulamalar için kullanışlıdır, çünkü bu kişiler dışarı ölçeklenebilirler. CPU yüzdesi, tüm örneklerde genel kullanım kullanımının iyi bir göstergesidir.

## <a name="metrics-granularity-and-retention-policy"></a>Ölçüm ayrıntı düzeyi ve bekletme ilkesi
Bir uygulama ve App Service planı için ölçümler, aşağıdaki granuya ve bekletme ilkeleriyle birlikte günlüğe kaydedilir ve hizmet tarafından toplanır:

* **Dakika** ayrıntı düzeyi ölçümleri 30 saat boyunca korunur.
* **Saat** ayrıntı düzeyi ölçümleri 30 gün boyunca tutulur.
* **Gün** ayrıntı düzeyi ölçümleri 30 gün boyunca tutulur.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Azure portal kotaları ve ölçümleri izleme
Bir uygulamayı etkileyen çeşitli kotalar ve ölçümlerin durumunu gözden geçirmek için [Azure Portal](https://portal.azure.com)gidin.

![Azure portal kota grafiği][quotas]

Kotaları bulmak için **ayarlar** > **Kotalar**' ı seçin. Grafik üzerinde şunları gözden geçirebilirsiniz: 
1. Kota adı.
1. Sıfırlama aralığı.
1. Geçerli sınırı.
1. Geçerli değeri.

Azure portal][metrics] ölçüm grafiği ![doğrudan **kaynak** sayfasından ölçümlere erişebilirsiniz. Grafiği özelleştirmek için: 
1. Grafiği seçin.
1. **Grafiği Düzenle ' yi**seçin.
1. **Zaman aralığını**düzenleyin.
1. **Grafik türünü**düzenleyin.
1. Göstermek istediğiniz ölçümleri düzenleyin.  

Ölçümler hakkında daha fazla bilgi edinmek için bkz. [hizmet ölçümlerini izleme](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Uyarılar ve otomatik ölçeklendirme
Bir uygulama veya App Service planı için ölçümler, uyarılara bağlanabilir. Daha fazla bilgi için bkz. [Uyarı bildirimleri alma](../monitoring-and-diagnostics/insights-alerts-portal.md).

Temel, standart veya Premium App Service planlarında barındırılan App Service uygulamalar otomatik ölçeklendirmeyi destekler. Otomatik ölçeklendirme ile App Service planı ölçümlerini izleyen kuralları yapılandırabilirsiniz. Kurallar, gerektiğinde ek kaynaklar sağlayabilen örnek sayısını artırabilir veya azaltabilir. Kurallar, uygulamanın aşırı sağlandığı durumlarda tasarruf etmenize de yardımcı olabilir.

Otomatik ölçeklendirme hakkında daha fazla bilgi için [](../monitoring-and-diagnostics/insights-how-to-scale.md) bkz. [Azure izleyici otomatik ölçeklendirme Için ölçeklendirme ve en iyi uygulamalar](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
---
title: Uygulamaları izleme
description: Azure portal kullanarak Azure App Service uygulamaları nasıl izleyeceğinizi öğrenin. Bildirilen kotaları ve ölçümleri anlayın.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: bf230032afe80680dc392c2a74da2a5aef381983
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586103"
---
# <a name="monitor-apps-in-azure-app-service"></a>Azure App Service uygulamaları izleme
[Azure App Service](./overview.md) , [Azure Portal](https://portal.azure.com)Web Apps, mobil ve API uygulamaları için yerleşik izleme işlevleri sağlar.

Azure portal, bir uygulama için *kotaları* ve *ölçümleri* gözden geçirebilir ve App Service planı ve *uyarıları* ve *Otomatik ölçeklendirme* kuralları tabanlı ölçümleri ayarlayabilirsiniz.

## <a name="understand-quotas"></a>Kotaları anlama

App Service barındırılan uygulamalar, kullanabilecekleri kaynaklar üzerinde belirli sınırlara tabidir. Sınırlar, uygulamayla ilişkili App Service planı tarafından tanımlanır.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Uygulama *ücretsiz* veya *paylaşılan* bir planda barındırılıyorsa, uygulamanın kullanabileceği kaynakların sınırları Kotalar tarafından tanımlanır.

Uygulama *temel*, *Standart* veya *Premium* bir planda barındırılıyorsa, kullandıkları kaynaklarla ilgili sınırlar, App Service planındaki *Boyut* (küçük, orta, büyük) ve *örnek sayısı* (1, 2, 3, vb.) ile ayarlanır.

Ücretsiz veya paylaşılan uygulamalar için kotalar şunlardır:

| Kota | Description |
| --- | --- |
| **CPU (kısa)** | Bu uygulama için, 5 dakikalık bir aralıkta izin verilen CPU miktarı. Bu kota her beş dakikada bir sıfırlanır. |
| **CPU (gün)** | Bu uygulama için bir günde izin verilen toplam CPU miktarı. Bu kota, her 24 saatte bir, gece yarısı UTC 'yi sıfırlar. |
| **Bellek** | Bu uygulama için izin verilen toplam bellek miktarı. |
| **Bant genişliği** | Bu uygulama için günde izin verilen giden bant genişliğinin toplam miktarı. Bu kota, her 24 saatte bir, gece yarısı UTC 'yi sıfırlar. |
| **Dosya Sistemi** | İzin verilen toplam depolama miktarı. |

*Temel*, *Standart* ve *Premium* 'da barındırılan uygulamalar Için geçerli olan tek Kota dosya sistemi 'dir.

Çeşitli App Service SKU 'Larının kullanabildiği belirli kotalar, sınırlar ve özellikler hakkında daha fazla bilgi için bkz. [Azure abonelik hizmeti sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Kota zorlaması

Bir uygulama *CPU (Short)*, *CPU (gün)* veya *bant genişliği* kotasını aşarsa, kota sıfırlanana kadar uygulama durdurulur. Bu süre boyunca, tüm gelen istekler HTTP 403 hatasına neden olacak.

![403 hata iletisi][http403]

Uygulama belleği kotası aşılırsa, uygulama geçici olarak durdurulur.

Dosya sistemi kotası aşılırsa, herhangi bir yazma işlemi başarısız olur. Yazma işlemi hatalarında günlüklere yazma işlemleri dahildir.

App Service planınızı yükselterek, kotalarınızı uygulamanızı artırabilir veya kaldırabilirsiniz.

## <a name="understand-metrics"></a>Ölçümleri anlama

> [!NOTE]
> **Dosya sistemi kullanımı** , genel olarak alınan yeni bir ölçümdür, uygulamanız bir App Service ortamı barındırılmadığı takdirde hiçbir veri beklenmez.
> 

> [!IMPORTANT]
> Ölçüm toplamalarının karışmasını önlemek için **Ortalama yanıt süresi** kullanım dışı olacaktır. **Yanıt süresini** değiştirme olarak kullanın.

> [!NOTE]
> Bir uygulama için ölçümler, uygulamanın SCM sitesine (kudu) yönelik istekleri içerir.  Bu, kudu kullanarak sitenin logstream 'i görüntüleme isteklerini içerir.  Logstream istekleri birkaç dakika yayılabileceğinden, Istek Süresi ölçümlerini etkileyecektir.  Bu ölçümler otomatik ölçeklendirme mantığı ile kullanılırken kullanıcılar bu ilişkiyi farkında olmalıdır.
> 

Ölçümler, uygulama veya App Service planının davranışı hakkında bilgi sağlar.

Bir uygulama için kullanılabilir ölçümler şunlardır:

| Metric | Açıklama |
| --- | --- |
| **Yanıt süresi** | Uygulamanın isteklere istek görmesi için geçen süre (saniye cinsinden). |
| **Ortalama yanıt süresi (kullanım dışı)** | Uygulama için geçen ortalama süre (saniye cinsinden). |
| **Ortalama bellek çalışma kümesi** | Uygulama tarafından, megabayt (MIB) cinsinden kullanılan ortalama bellek miktarı. |
| **Bağlantılar** | Korumalı alan içindeki ilişkili yuva sayısı (w3wp.exe ve alt işlemi).  Bağlama yuvası, bind ()/Connect () API 'Leri çağırarak ve bir yuva CloseHandle ()/Closesocket () ile kapatılana kadar kalır. |
| **CPU süresi** | Uygulama tarafından saniye cinsinden tüketilen CPU miktarı. Bu ölçüm hakkında daha fazla bilgi için bkz. [CPU saati vs CPU yüzdesi](#cpu-time-vs-cpu-percentage). |
| **Geçerli derlemeler** | Bu uygulamadaki tüm AppDomain 'ler genelinde yüklenen derlemelerin geçerli sayısı. |
| **Içindeki veriler** | MIB içinde uygulama tarafından tüketilen gelen bant genişliği miktarı. |
| **Giden veriler** | MIB içinde uygulama tarafından tüketilen giden bant genişliği miktarı. |
| **Dosya sistemi kullanımı** | Depolama payı için bayt cinsinden kullanım miktarı. |
| **Gen 0 çöp koleksiyonları** | Uygulama işleminin başlangıcından bu yana nesil 0 nesnelerinin atık olarak toplandığı sayı. Daha yüksek nesil GCs, tüm düşük nesil GCs 'leri içerir.|
| **Gen 1 çöp koleksiyonları** | Uygulama işleminin başlangıcından bu yana 1. nesil nesnelerin atık olarak toplandığı sayı. Daha yüksek nesil GCs, tüm düşük nesil GCs 'leri içerir.|
| **Gen 2 çöp koleksiyonları** | Oluşturma 2 nesnelerinin, uygulama işleminin başlangıcından bu yana atık olarak toplandığı sayı.|
| **Tanıtıcı Sayısı** | Uygulama işlemi tarafından şu anda açık olan tanıtıcı sayısının toplam sayısı.|
| **Durum denetimi durumu** | App Service planındaki uygulamanın örnekleri genelinde ortalama sistem durumu.|
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
| **İstekler** | Elde edilen HTTP durum kodundan bağımsız olarak isteklerin toplam sayısı. |
| **Uygulama kuyruğundaki istekler** | Uygulama istek sırasındaki isteklerin sayısı.|
| **İş parçacığı sayısı** | Uygulama işleminde Şu anda etkin olan iş parçacıklarının sayısı.|
| **Toplam uygulama etki alanları** | Bu uygulamada yüklü olan AppDomain 'in geçerli sayısı.|
| **Toplam yüklenmeyen uygulama etki alanları** | Uygulamanın başlangıcından bu yana bellekten kaldırılan toplam AppDomain sayısı.|


App Service planı için kullanılabilir ölçümler şunlardır:

> [!NOTE]
> App Service planı ölçümleri yalnızca *temel*, *Standart* ve *Premium* katmanlardaki planlar için kullanılabilir.
> 

| Metric | Açıklama |
| --- | --- |
| **CPU yüzdesi** | Planın tüm örnekleri genelinde kullanılan ortalama CPU. |
| **Bellek yüzdesi** | Planın tüm örnekleri genelinde kullanılan ortalama bellek. |
| **Içindeki veriler** | Planın tüm örnekleri genelinde kullanılan ortalama gelen bant genişliği. |
| **Giden veriler** | Planın tüm örneklerinde kullanılan ortalama giden bant genişliği. |
| **Disk kuyruğu uzunluğu** | Depolama üzerinde sıraya alınan okuma ve yazma isteklerinin ortalama sayısı. Yüksek disk kuyruğu uzunluğu, aşırı disk g/ç nedeniyle yavaşlabilen bir uygulamanın göstergesidir. |
| **Http kuyruğu uzunluğu** | Yerine getirilmeden önce kuyruğa oturmak zorunda olan HTTP isteklerinin ortalama sayısı. Yüksek veya artan bir HTTP kuyruğu uzunluğu, ağır yük altında bir planın belirtisidir. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU süresi vs CPU yüzdesi
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

CPU kullanımını yansıtan iki ölçüm vardır:

**CPU süresi**: ücretsiz veya paylaşılan planlarda barındırılan uygulamalar için yararlıdır, çünkü kotalarından biri uygulama tarafından kullanılan CPU dakikalarında tanımlanmıştır.

**CPU yüzdesi**: temel, standart ve Premium planlarında barındırılan uygulamalar için kullanışlıdır, çünkü bu kişiler dışarı ölçeklenebilirler. CPU yüzdesi, tüm örneklerde genel kullanım kullanımının iyi bir göstergesidir.

## <a name="metrics-granularity-and-retention-policy"></a>Ölçüm ayrıntı düzeyi ve bekletme ilkesi
Bir uygulama ve App Service planı için ölçümler, hizmet tarafından günlüğe kaydedilir ve toplanır ve [Bu kurallara göre saklanır](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics).

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Azure portal kotaları ve ölçümleri izleme
Bir uygulamayı etkileyen çeşitli kotalar ve ölçümlerin durumunu gözden geçirmek için [Azure Portal](https://portal.azure.com)gidin.

![Azure portal kota grafiği][quotas]

Kotaları bulmak için, **Ayarlar**  >  **Kotalar**' ı seçin. Grafik üzerinde şunları gözden geçirebilirsiniz: 
1. Kota adı.
1. Sıfırlama aralığı.
1. Geçerli sınırı.
1. Geçerli değeri.

![Azure portal ölçüm grafiği ][metrics] doğrudan kaynak **genel bakış** sayfasından ölçümlere erişebilirsiniz. Burada bazı uygulama ölçümlerini temsil eden grafikler görürsünüz.

Bu grafiklerin herhangi birine tıkladığınızda, özel grafikler oluşturabileceğiniz, farklı ölçümleri Sorgulayabileceğiniz ve çok daha birçok Ölçüm görünümü uygulanır. 

Ölçümler hakkında daha fazla bilgi edinmek için bkz. [hizmet ölçümlerini izleme](../azure-monitor/data-platform.md).

## <a name="alerts-and-autoscale"></a>Uyarılar ve otomatik ölçeklendirme
Bir uygulama veya App Service planı için ölçümler, uyarılara bağlanabilir. Daha fazla bilgi için bkz. [Uyarı bildirimleri alma](../azure-monitor/alerts/alerts-classic-portal.md).

Temel veya daha yüksek App Service planlarında barındırılan App Service uygulamalar otomatik ölçeklendirmeyi destekler. Otomatik ölçeklendirme ile App Service planı ölçümlerini izleyen kuralları yapılandırabilirsiniz. Kurallar, gerektiğinde ek kaynaklar sağlayabilen örnek sayısını artırabilir veya azaltabilir. Kurallar, uygulamanın aşırı sağlandığı durumlarda tasarruf etmenize de yardımcı olabilir.

Otomatik ölçeklendirme hakkında daha fazla bilgi için [](../azure-monitor/autoscale/autoscale-get-started.md) bkz. [Azure izleyici otomatik ölçeklendirme Için ölçeklendirme ve en iyi uygulamalar](../azure-monitor/autoscale/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png

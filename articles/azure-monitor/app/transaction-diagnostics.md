---
title: Azure Application Insights Işlem tanılama | Microsoft Docs
description: Uçtan uca işlem tanılamayı Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 1ed3713fe4a6c9403be13f444d0409af459a1e70
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899564"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Birleştirilmiş çapraz bileşen işlem tanılaması

Birleşik tanılama deneyimi, sunucu tarafı telemetrisini tüm Application Insights izlenen bileşenlerinizin tamamında tek bir görünümde otomatik olarak ilişkilendirir. Ayrı izleme anahtarlarına sahip birden fazla kaynağınız varsa bu değildir. Application Insights, temel ilişkiyi algılar ve bir işlem yavaşlama veya hatasına neden olan uygulama bileşenini, bağımlılığı veya özel durumu kolayca tanılamanıza olanak sağlar.

## <a name="what-is-a-component"></a>Bileşen nedir?

Bileşenler, dağıtılmış/mikro hizmetler uygulamanızın bağımsız olarak dağıtılabilir parçalarından oluşur. Geliştiriciler ve işlemler ekiplerinde kod düzeyinde görünürlük veya bu uygulama bileşenleri tarafından oluşturulan telemetri erişimi vardır.

* Bileşenler, takımınızın/kuruluşunuzun erişimi olmayan (kod veya telemetri) SQL, EventHub vb. gibi "gözlemlenen" dış bağımlılıklardan farklıdır.
* Bileşenler herhangi bir sayıda sunucu/rol/kapsayıcı örneği üzerinde çalışır.
* Bileşenler Application Insights izleme anahtarlarına (abonelikler farklıysa bile) veya tek bir Application Insights izleme anahtarına rapor veren farklı rollere sahip olabilir. Yeni deneyim, nasıl ayarlandıklarından bağımsız olarak tüm bileşenler genelinde ayrıntıları gösterir.

> [!NOTE]
> * **İlgili öğe bağlantıları eksik mı?** Tüm ilgili telemetri, sol taraftaki [en üst](#cross-component-transaction-chart) ve [alt](#all-telemetry-with-this-operation-id) bölümlerde bulunur. 

## <a name="transaction-diagnostics-experience"></a>İşlem tanılama deneyimi
Bu görünümde dört temel bölüm bulunur: sonuçlar listesi, bir çapraz bileşen işlem grafiği, bu işlemle ilgili tüm Telemetriyi bir zaman sırası listesi ve sol taraftaki seçili telemetri öğesi için Ayrıntılar bölmesi.

![Anahtar parçalar](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Çapraz bileşen işlem grafiği

Bu grafik, bileşenler arasındaki isteklerin ve bağımlılıkların süresi için yatay çubuklarla bir zaman çizelgesi sağlar. Toplanan tüm özel durumlar da zaman çizelgesinde işaretlenir.

* Bu grafikteki en üst satır, bu işlemde çağrılan ilk bileşene gelen istek olan giriş noktasını temsil eder. Süre, işlemin tamamlaması için geçen toplam süredir.
* Dış bağımlılıklara yapılan çağrılar, bağımlılık türünü temsil eden simgelerle birlikte, daraltılamayan basit satırlardır.
* Diğer bileşenlere yapılan çağrılar daraltılabilir satırlardır. Her satır, bileşende çağrılan belirli bir işleme karşılık gelir.
* Varsayılan olarak, seçtiğiniz istek, bağımlılık veya özel durum sağ tarafta görüntülenir.
* [Ayrıntılarını sağ](#details-of-the-selected-telemetry)tarafta görmek için herhangi bir satır seçin. 

> [!NOTE]
> Diğer bileşenlere yapılan çağrılar iki satıra sahiptir: bir satır, çağıran bileşeninden giden çağrıyı (bağımlılık) temsil eder ve diğer satır çağrılan bileşendeki gelen isteğe karşılık gelir. Süre çubuklarının önde gelen simgesi ve farklı stillendirme, aralarında ayrım yapmanıza yardımcı olur.

## <a name="all-telemetry-with-this-operation-id"></a>Bu Işlem kimliği ile tüm telemetri

Bu bölümde, bu işlemle ilgili tüm Telemetriyi bir zaman dizisinde düz liste görünümü gösterilmektedir. Ayrıca, özel olayları ve işlem grafiğinde görüntülenmeyen izlemeleri gösterir. Bu listeyi, belirli bir bileşen/çağrı tarafından oluşturulan telemetriye filtreleyebilirsiniz. İlgili [ayrıntıları sağda](#details-of-the-selected-telemetry)görmek için bu listedeki herhangi bir telemetri öğesini seçebilirsiniz.

![Tüm Telemetriyi zaman dizisi](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Seçili Telemetriyi ayrıntıları

Bu daraltılabilir bölmede, işlem grafiğindeki veya listedeki seçili öğelerin ayrıntıları gösterilir. "Tümünü göster", toplanan tüm standart öznitelikleri listeler. Özel öznitelikler standart kümesinin altında ayrı olarak listelenir. "..." Seçeneğine tıklayın izlemeyi kopyalamak için bir seçenek almak üzere yığın izleme penceresinin altında. "Profil Oluşturucu izlemelerini aç" veya "hata ayıklama anlık görüntüsünü aç", ilgili ayrıntı bölmelerinde kod düzeyi tanılamayı gösterir.

![Özel durum ayrıntısı](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Arama sonuçları

Bu daraltılabilir bölmede, filtre ölçütlerine uyan diğer sonuçlar gösterilir. Yukarıda listelenen 3 bölümden ilgili ayrıntıları güncelleştirmek için herhangi bir sonuca tıklayın. Örnekleme herhangi bir uygulamada etkin olsa bile tüm bileşenlerden ayrıntıları sağlamak için en olası örnekleri bulmaya çalışırız. Bunlar "önerilen" örnekler olarak gösterilir.

![Arama sonuçları](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profil Oluşturucu ve anlık görüntü hata ayıklayıcısı

[Application Insights profil oluşturucu](../../azure-monitor/app/profiler.md) veya [anlık görüntü hata ayıklayıcısı](snapshot-debugger.md) , performans ve başarısızlık sorunlarının kod düzeyinde tanılamada yardımcı olur. Bu deneyimle, tek bir tıklama ile herhangi bir bileşenden profil oluşturucu izlemelerini veya anlık görüntüleri görebilirsiniz.

Profil oluşturucuyu çalışma konusunda alamazsanız lütfen **serviceprofilerhelp\@Microsoft.com** başvurun

Snapshot Debugger çalışmadıysanız, lütfen **anlık görüntüyle iletişime geçin\@Microsoft.com**

![Profil Oluşturucu tümleştirmesi](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>SSS

*Grafik üzerinde tek bir bileşen görüyorum ve diğerleri yalnızca dış bağımlılıklar olarak gösterildiğimde bu bileşenlere ne olduğuna ilişkin hiçbir ayrıntı yoktur.*

Olası nedenler:

* Diğer bileşenler Application Insights ile işaretlenmiş mı?
* En son kararlı Application Insights SDK 'sını kullanıyor musunuz?
* Bu bileşenler Application Insights kaynakları ayırdıysanız, telemetrisine erişiminizin olması gerekir mi?

Erişiminiz varsa ve bileşenler en son Application Insights SDK 'lar ile birlikte işaretlenmiş ise, en iyi geri bildirim kanalı üzerinden bize bilgi verin.

*Bağımlılıklar için yinelenen satırlar görüyorum. Bu beklensin mi?*

Şu anda gelen istekten ayrı giden bağımlılık çağrısını gösteriyoruz. Genellikle, iki çağrı, ağ gidiş dönüş nedeniyle yalnızca Duration değeri farklı olacak şekilde aynı şekilde görünür. Süre çubuklarının önde gelen simgesi ve farklı stillendirme, aralarında ayrım yapmanıza yardımcı olur. Verilerin bu sunumu kafa karıştırıcı mı? Görüşlerinizi bize iletin!

*Farklı bileşen örneklerinde saatin ne kadar eğilen?*

Zaman çizelgeleri, işlem grafiğindeki saat eğelerine göre ayarlanır. Tam zaman damgasını Ayrıntılar bölmesinde veya analiz kullanarak görebilirsiniz.

*Yeni deneyim neden birçok ilgili öğe sorgusu eksik?*

Bu tasarım gereğidir. Tüm bileşenler genelinde tüm ilgili öğeler zaten sol tarafta (üst ve alt bölümler) kullanılabilir. Yeni deneyim, sol taraftaki iki ilişkili öğeye sahiptir: Bu olaydan ve sonrasında beş dakikadan önceki ve sonraki tüm telemetri ve Kullanıcı zaman çizelgesi.

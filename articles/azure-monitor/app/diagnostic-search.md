---
title: Azure Application Insights arama kullanma | Microsoft Docs
description: Web uygulamanız tarafından gönderilen ham Telemetriyi arayın ve filtreleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/30/2019
ms.openlocfilehash: 77cd0a8d0c1a93e7dc1db931e987a172d31978ef
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678010"
---
# <a name="using-search-in-application-insights"></a>Application Insights arama kullanma

Arama, sayfa görünümleri, özel durumlar veya Web istekleri gibi tek tek telemetri öğelerini bulmak ve araştırmak için kullandığınız [Application Insights](../../azure-monitor/app/app-insights-overview.md) özelliğidir. Ve kodlandığı günlük izlemelerini ve olayları görüntüleyebilirsiniz.

(Verileriniz üzerinde daha karmaşık sorgular için [analiz](../../azure-monitor/log-query/get-started-portal.md)kullanın.)

## <a name="where-do-you-see-search"></a>Aramayı nerede görüyorsunuz?

### <a name="in-the-azure-portal"></a>Azure portal

Tanılama aramasını uygulamanızın Application Insights genel bakış sekmesinden açabilirsiniz (üst çubukta bulunan) veya sol tarafta Araştır ' ın altında.

![Arama sekmesi](./media/diagnostic-search/view-custom-events.png)

Telemetri öğelerinin bir listesini, sunucu isteklerini, sayfa görünümlerini, kodlandığı özel olayları ve benzerlerini görmek için olay türleri ' açılan menüsüne gidin. Sonuçlar ' listesinin en üstünde, zaman içinde olayların sayısını gösteren bir Özet grafiktir.

Yeni olaylar almak için açılan menüyü veya yenilemeyi seçin.

### <a name="in-visual-studio"></a>Visual Studio’da

Visual Studio 'da Ayrıca bir Application Insights arama penceresi de vardır. Bu, hata ayıklama yaptığınız uygulama tarafından oluşturulan telemetri olaylarını görüntülemek için yararlıdır. Ancak, Azure portal yayımlanan uygulamanızdan toplanan olayları da gösterebilir.

Visual Studio 'da arama penceresini açın:

![Visual Studio açık Application Insights arama](./media/diagnostic-search/32.png)

Arama penceresinde web portalına benzer özellikler vardır:

![Visual Studio Application Insights arama penceresi](./media/diagnostic-search/34.png)

Izleme Işlemi sekmesi bir istek veya sayfa görünümü açtığınızda kullanılabilir. ' İşlem ', tek bir istek veya sayfa görünümü ile ilişkili olayların bir dizisidir. Örneğin, bağımlılık çağrıları, özel durumlar, izleme günlükleri ve özel olaylar tek bir işlemin parçası olabilir. Izleme Işlemi sekmesi, istek veya sayfa görünümüyle ilgili olarak bu olayların zamanlamasını ve süresini grafik olarak gösterir.

## <a name="inspect-individual-items"></a>Ayrı öğeleri İnceleme

Anahtar alanları ve ilgili öğeleri görmek için herhangi bir telemetri öğesini seçin.

![Tek bir bağımlılık isteğinin ekran görüntüsü](./media/diagnostic-search/telemetry-item.png)

Bu, uçtan uca işlem ayrıntıları görünümünü başlatacaktır.

## <a name="filter-event-types"></a>Olay türlerini filtrele

Olay türleri ' açılan menüsünü açın ve görmek istediğiniz olay türlerini seçin. (Daha sonra, filtreleri geri yüklemek istiyorsanız Sıfırla ' ya tıklayın.)

Olay türleri şunlardır:

* TrackTrace, log4Net, NLog ve System. Diagnostics. Trace çağrıları dahil olmak üzere[tanılama günlüklerini](../../azure-monitor/app/asp-net-trace-logs.md) **izleme**  - .
* **İstek** -sunucu uygulamanız tarafından alınan, sayfalar, betikler, görüntüler, stil dosyaları ve veriler dahil http istekleri. Bu olaylar, istek ve yanıt genel bakış grafiklerini oluşturmak için kullanılır.
* Sayfa görüntüleme raporları oluşturmak için kullanılan[Web istemcisi tarafından gönderilen telemetri](../../azure-monitor/app/javascript.md) -  **sayfa görünümü** .
* **Özel olay** - [kullanımı Izlemek](../../azure-monitor/app/api-custom-events-metrics.md)için trackevent () çağrıları eklediyseniz, burada arama yapabilirsiniz.
* **Özel durum** - [sunucuda](../../azure-monitor/app/asp-net-exceptions.md)yakalanamayan özel durumlar ve trackexception () kullanarak oturum açmalarını sağlayabilirsiniz.
* **Bağımlılık**  - ,[sunucu uygulamanızdan](../../azure-monitor/app/asp-net-dependencies.md) REST API 'leri veya veritabanları gibi diğer HIZMETLERE ve [istemci kodunuzun](../../azure-monitor/app/javascript.md)Ajax çağrılarına çağrı yapılır.
* **Kullanılabilirlik-** [kullanılabilirlik testlerinin](../../azure-monitor/app/monitor-web-app-availability.md)sonuçları.

## <a name="filter-on-property-values"></a>Özellik değerlerini filtrele

Olaylarını özelliklerinin değerlerine göre filtreleyebilirsiniz. Kullanılabilir özellikler, seçtiğiniz olay türlerine bağlıdır. Filtre simgesine tıklayın ![Filtre simgesi](./media/diagnostic-search/filter-icon.png) başlamak için.

Belirli bir özelliğin hiçbir değer seçilmesi, tüm değerleri seçerken aynı etkiye sahip olur. Bu özelliğin filtrelenmesini devre dışı bırakır.

Filtre değerlerinin sağında bulunan sayımların, geçerli filtrelenmiş kümesinde kaç tane oluşum olduğunu gösterir.

## <a name="find-events-with-the-same-property"></a>Aynı özelliğe sahip olayları bulma

Aynı özellik değerine sahip tüm öğeleri bulmak için arama çubuğuna yazın veya filtre sekmesindeki Özellikler ' e baktığınızda onay kutusuna tıklayın.

![Filtre sekmesindeki bir özelliğin onay kutusuna tıklayın](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Verilerde arama

> [!NOTE]
> Daha karmaşık sorgular yazmak için arama dikey penceresinin en üstünden [**günlükleri (Analiz)** ](../../azure-monitor/log-query/get-started-portal.md) açın.
>

Özellik değerlerinin herhangi birinde terimleri arayabilirsiniz. Bu özellik değerleri ile [özel olaylar](../../azure-monitor/app/api-custom-events-metrics.md) yazdıysanız yararlı olur.

Daha kısa bir aralıktaki aramalar daha hızlı olduğundan bir zaman aralığı ayarlamak isteyebilirsiniz.

![Tanılama aramasını aç](./media/diagnostic-search/search-property.png)

Alt dizeler değil, tüm sözcükleri arayın. Özel karakterleri kapsamak için tırnak işaretleri kullanın.

| Dize | *Bulunamadı* | Bulunamaz |
| --- | --- | --- |
| HomeController. hakkında |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Birleşik Devletler|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Kullanabileceğiniz arama ifadeleri aşağıda verilmiştir:

| Örnek sorgu | Etki |
| --- | --- |
| `apple` |Alanları "Apple" sözcüğünü içeren zaman aralığındaki tüm olayları bul |
| `apple AND banana` <br/>`apple banana` |Her iki sözcüğü de içeren olayları bulun. "Ve" değil, sermaye "ve" kullanın. <br/>Kısa biçim. |
| `apple OR banana` |Word içeren olayları bulun. "Veya" değil, "veya" kullanın. |
| `apple NOT banana` |Bir sözcük içeren ancak diğerini olmayan olayları bulun. |

## <a name="sampling"></a>Örnekleme

Uygulamanız büyük miktarda telemetri oluşturuyorsa (ve ASP.NET SDK sürümünü 2.0.0-Beta3 veya üzerini kullanıyorsanız), uyarlamalı örnekleme modülü, yalnızca bir dizi olayın temsili bir kısmını göndererek portala gönderilen hacmi otomatik olarak azaltır. Ancak, aynı istekle ilgili olaylar, bir grup olarak seçilir veya seçimden kaldırılır, böylece ilgili olaylar arasında gezinebilmenizi sağlayabilirsiniz.

[Örnekleme hakkında bilgi edinin](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>İş öğesi oluştur

GitHub veya Azure DevOps 'da herhangi bir telemetri öğesinin ayrıntılarından bir hata oluşturabilirsiniz.

Herhangi bir telemetri öğesine tıklayıp **iş öğesi oluştur**' u seçerek uçtan uca işlem ayrıntısı görünümüne gidin.

![Yeni Iş öğesi ' ne tıklayın, alanları düzenleyin ve ardından Tamam ' a tıklayın.](./media/diagnostic-search/work-item.png)

Bunu ilk kez yaptığınızda Azure DevOps kuruluşunuz ve projeniz için bir bağlantı yapılandırmanız istenir.

(Iş öğeleri sekmesinde bağlantıyı da yapılandırabilirsiniz.)

## <a name="send-more-telemetry-to-application-insights"></a>Application Insights için daha fazla telemetri gönderin

Application Insights SDK tarafından gönderilen kullanıma hazır telemetriye ek olarak şunları yapabilirsiniz:

* [.Net](../../azure-monitor/app/asp-net-trace-logs.md) veya [Java](../../azure-monitor/app/java-trace-logs.md)'daki en sevdiğiniz günlük çatısı için günlük izlemelerini yakalayın. Bu, günlük izlemelerinizi arayabilir ve bunları sayfa görünümleri, özel durumlar ve diğer olaylarla ilişkilendirebilmeniz anlamına gelir.
* Özel olaylar, sayfa görünümleri ve özel durumlar göndermek için [kod yazın](../../azure-monitor/app/api-custom-events-metrics.md) .

[Application Insights için günlüklerin ve özel telemetrinin nasıl gönderileceğini öğrenin](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>soru-cevap &

### <a name="limits"></a>Ne kadar veri tutuluyor?

[Limitlerin özetine](../../azure-monitor/app/pricing.md#limits-summary)bakın.

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Sunucu isteklerim ' de veri gönderme işlemini nasıl görebilirim?

POST verilerini otomatik olarak günlüğe vermedik, ancak [Tracktrace veya log çağrılarını](../../azure-monitor/app/asp-net-trace-logs.md)kullanabilirsiniz. POST verilerini ileti parametresine koyun. Aynı şekilde, özellikleri filtreleyebileceğiniz şekilde filtreleyebilirsiniz, ancak boyut sınırı daha uzundur.

## <a name="add"></a>Sonraki adımlar

* [Analytics 'te karmaşık sorgular yazma](../../azure-monitor/log-query/get-started-portal.md)
* [Application Insights için Günlükler ve özel telemetri gönder](../../azure-monitor/app/asp-net-trace-logs.md)
* [Kullanılabilirlik ve yanıt verme testlerini ayarlama](../../azure-monitor/app/monitor-web-app-availability.md)
* [Sorun giderme](../../azure-monitor/app/troubleshoot-faq.md)

---
title: Azure Izleyici çalışma kitapları veri kaynakları | Microsoft docs
description: Birden çok veri kaynağından oluşturulan önceden oluşturulmuş ve özel parametreli Azure Izleyici çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 862c6c5253c1bb4481476b67c7cfb203c2568e24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700584"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Izleyici çalışma kitapları veri kaynakları

Çalışma kitapları çok sayıda veri kaynağı ile uyumludur. Bu makale, şu anda Azure Izleyici çalışma kitapları için kullanılabilen veri kaynakları boyunca size yol gösterecektir.

## <a name="logs"></a>Günlükler

Çalışma kitapları aşağıdaki kaynaklardan günlükleri sorgulamaya izin verir:

* Azure Izleyici günlükleri (Application Insights kaynakları ve Log Analytics çalışma alanları.)
* Kaynak merkezli veriler (etkinlik günlükleri)

Çalışma kitabı yazarları, temel kaynak verilerini dönüştüren KQL sorgularını, metin, grafikler veya kılavuzlar olarak görselleştirilebilen bir sonuç kümesi seçmek için kullanabilir.

![Çalışma kitabı günlüğü rapor arabiriminin ekran görüntüsü](./media/workbooks-data-sources/logs.png)

Çalışma kitabı yazarları, gerçekten Birleşik bir zengin raporlama deneyimi oluşturarak birden çok kaynak genelinde kolayca sorgu yapabilir.

## <a name="metrics"></a>Ölçümler

Azure kaynakları, çalışma kitapları aracılığıyla erişilebilen [ölçümleri](../essentials/data-platform-metrics.md) yayar. Ölçümlere, hedef kaynakları, istenen ölçümleri ve bunların toplamasını belirtmenize olanak tanıyan özelleştirilmiş bir denetim aracılığıyla çalışma kitaplarından erişilebilir. Bu veriler daha sonra grafiklerde veya kılavuzlarda çizilebilirler.

![CPU kullanımının çalışma kitabı ölçüm grafiklerinin ekran görüntüsü](./media/workbooks-data-sources/metrics-graph.png)

![Çalışma kitabı ölçümleri arabiriminin ekran görüntüsü](./media/workbooks-data-sources/metrics.png)

## <a name="azure-resource-graph"></a>Azure Kaynak Grafı

Çalışma kitapları, Azure Kaynak Grafiği (ARG) kullanarak kaynakları ve bunların meta verilerini sorgulamayı destekler. Bu işlevsellik öncelikle raporlar için özel sorgu kapsamları oluşturmak için kullanılır. Kaynak kapsamı, bağımsız değişken tarafından desteklenen bir KQL-alt kümesi ile ifade edilir ve genellikle yaygın kullanım örnekleri için yeterlidir.

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için, Azure Kaynak Grafiği ' ni seçmek üzere sorgu türü açılan listesini kullanın ve hedeflenecek abonelikleri seçin. İlgi çekici bir kaynak alt kümesi seçen ARG KQL-alt kümesini eklemek için sorgu denetimini kullanın.

![Azure Kaynak Grafiği KQL sorgusunun ekran görüntüsü](./media/workbooks-data-sources/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

Çalışma kitabı Azure Resource Manager REST işlemlerini destekler. Bu, kendi yetkilendirme üst bilgi belirtecinizi sağlamaya gerek kalmadan management.azure.com uç noktasını sorgulama olanağına sahip olmak için izin verir.

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için, Azure Resource Manager seçmek için veri kaynağı açılır listesini kullanın. Http yöntemi, URL yolu, üst bilgiler, URL parametreleri ve/veya gövde gibi uygun parametreleri sağlayın.

> [!NOTE]
> Yalnızca `GET` , `POST` ve `HEAD` işlemleri şu anda destekleniyor.

## <a name="azure-data-explorer"></a>Azure Veri Gezgini

Artık çalışma kitapları, güçlü [kusto](/azure/kusto/query/index) sorgu diliyle [Azure Veri Gezgini](/azure/data-explorer/) kümelerinden sorgulama desteğine sahiptir.

![Kusto sorgu penceresinin ekran görüntüsü](./media/workbooks-data-sources/data-explorer.png)

## <a name="workload-health"></a>İş yükü sistem durumu

Azure Izleyici, Windows veya Linux konuk işletim sistemlerinin kullanılabilirliğini ve performansını önceden izleyerek izleyen işlevlere sahiptir. Azure Izleyici, temel bileşenleri ve bunların ilişkilerini, bu bileşenlerin sistem durumunu ölçme ölçütlerini ve sağlıksız bir durum algılandığında hangi bileşenlerin sizi uyarmasını sağlar. Çalışma kitapları, kullanıcıların zengin etkileşimli raporlar oluşturmak için bu bilgileri kullanmasına izin verir.

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için, **sorgu türü** açılan listesini kullanarak Iş yükü sistem durumunu seçin ve hedeflenen abonelik, kaynak grubu veya VM kaynakları ' nı seçin. Analiz gereksinimleriniz için sistem durumu olaylarının ilgi çekici bir alt kümesini seçmek üzere durum filtre açılan listelerini kullanın.

![Uyarı sorgusunun ekran görüntüsü](./media/workbooks-data-sources/workload-health.png)

## <a name="azure-resource-health"></a>Azure Kaynak durumu

Çalışma kitapları, Azure Kaynak durumu almayı ve zengin, etkileşimli sistem durumu raporları oluşturmak için diğer veri kaynaklarıyla birleştirmeyi destekler

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için **sorgu türü** açılan listesini kullanarak Azure sistem durumu ' nu seçin ve hedeflenecek kaynakları seçin. Analiz gereksinimleriniz için kaynak sorunlarının ilginç bir alt kümesini seçmek üzere durum filtre açılan listelerini kullanın.

![Durum filtre listelerini gösteren uyarı sorgusunun ekran görüntüsü.](./media/workbooks-data-sources/resource-health.png)

## <a name="change-analysis-preview"></a>Değişiklik Analizi (Önizleme)

Veri kaynağı olarak [Uygulama değişikliği analizini](../app/change-analysis.md) kullanarak bir sorgu denetimi yapmak Için, *veri kaynağı* açılan listesini kullanın ve *değişiklik Analizi (Önizleme)* öğesini seçin ve tek bir kaynak seçin. Son 14 güne kadar olan değişiklikler görüntülenebilir. *Düzey* açılan listesi, "önemli", "normal" ve "gürültülü" değişiklikler arasında filtreleme yapmak için kullanılabilir ve bu açılan [liste açılan çalışma](workbooks-dropdowns.md)kitabı parametrelerini destekler.

> [!div class="mx-imgBorder"]
> ![Değişiklik analizini içeren çalışma kitabının ekran görüntüsü](./media/workbooks-data-sources/change-analysis-data-source.png)

## <a name="merge-data-from-different-sources"></a>Farklı kaynaklardaki verileri birleştirme

Öngörüler deneyimini geliştiren farklı kaynaklardaki verileri bir araya getirmek genellikle gereklidir. Bir örnek, ilgili ölçüm verileriyle etkin uyarı bilgilerini genişleterek bir örnektir. Bu, kullanıcıların yalnızca efekti (etkin bir uyarı) değil, olası nedenleri (örneğin, yüksek CPU kullanımı) görmesini sağlar. İzleme etki alanında, önceliklendirme ve Diagnostic iş akışı için genellikle kritik olan çok sayıda benzer veri kaynağı vardır.

Çalışma kitapları yalnızca farklı veri kaynaklarının sorgulanmasına izin vermez, ancak zengin Öngörüler sağlamak üzere verileri birleştirmenizi veya birleştirmeyi sağlayan basit denetimler de sağlar. `merge`Denetim, bunu başarmanın yoludur.

Aşağıdaki örnek, Log Analytics VM performans verileriyle birlikte uyarı verilerini birleştirerek zengin Öngörüler Kılavuzu elde edin.

> [!div class="mx-imgBorder"]
> ![Uyarı ve Log Analytics verilerini birleştiren bir birleştirme denetimiyle çalışma kitabının ekran görüntüsü](./media/workbooks-data-sources/merge-control.png)

Çalışma kitapları çeşitli birleştirmeleri destekler:

* İç benzersiz birleşim
* Tam iç birleşim
* Tam dış birleştirme
* Sol dış birleştirme
* Sağ dış birleştirme
* Sol yarı ekleme
* Sağ yarı ekleme
* Sol katılımı önleme
* Sağ Anti-ayrılma
* Birleşim
* Yinelenen tablo

## <a name="json"></a>JSON

JSON sağlayıcısı, statik JSON içeriğinden bir sorgu sonucu oluşturmanıza olanak sağlar. Statik değerlerin açılan menü parametreleri oluşturmak için parametrelerde en yaygın olarak kullanılır. Basit JSON dizileri veya nesneleri otomatik olarak kılavuz satırlarına ve sütunlara dönüştürülür.  Daha özel davranışlar için, sütunları yapılandırmak için sonuçlar sekmesini ve JSONPath ayarlarını kullanabilirsiniz.

Bu sağlayıcı [Jsonpath](workbooks-jsonpath.md)'i destekliyor.

## <a name="alerts-preview"></a>Uyarılar (önizleme)

> [!NOTE]
> Azure uyarı bilgilerini sorgulamak için önerilen yol, tabloyu sorgulayarak [Azure Kaynak Grafiği](#azure-resource-graph) veri kaynağını kullanmaktır `AlertsManagementResources` .
>
> Örnekler için bkz. [Azure Kaynak Grafiği tablosu başvurusu](../../governance/resource-graph/reference/supported-tables-resources.md)veya [Uyarılar şablonu](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) .
>
> Yazarlar, bağımsız değişken kullanmaya geçiş yaparken, uyarılar veri kaynağı bir süre için kullanılabilir kalır. Bu veri kaynağının şablonlarda kullanılması önerilmez. 

Çalışma kitapları, kullanıcıların kaynaklarıyla ilgili etkin uyarıları görselleştirmesine olanak tanır. Sınırlamalar: uyarılar veri kaynağı, kaynakları sorgulamak için abonelik için okuma erişimi gerektirir ve daha yeni uyarı türlerini gösteremeyebilir. 

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için, _veri kaynağı_ açılır listesini kullanarak Uyarılar ' ı _(Önizleme)_ seçin ve hedeflenecek abonelikleri, kaynak gruplarını veya kaynakları seçin. Analitik gereksinimleriniz için uyarıların ilginç bir alt kümesini seçmek üzere uyarı filtresi açılan listelerini kullanın.

## <a name="custom-endpoint"></a>Özel uç nokta

Çalışma kitapları, herhangi bir dış kaynaktan veri almayı destekler. Verileriniz Azure dışında yaşar, bu veri kaynağı türünü kullanarak çalışma kitaplarına taşıyabilirsiniz.

Bir sorgu denetiminin bu veri kaynağını kullanmasını sağlamak için, _veri kaynağı_ açılır listesini kullanarak _Özel uç nokta_' ı seçin. ,, `Http method` `url` `headers` , `url parameters` Ve/ `body` gibi uygun parametreleri sağlayın. Veri kaynağınızın [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 'yi desteklediğinden emin olun, aksi takdirde istek başarısız olur.

Şablonları kullanırken güvenilmeyen konaklara otomatik olarak çağrı yapmayı önlemek için, kullanıcının kullanılan konakları güvenilir olarak işaretlemesi gerekir. Bu işlem, _güvenilir değil Ekle_ düğmesine tıklayarak veya çalışma kitabı ayarları 'nda güvenilir bir ana bilgisayar olarak eklenerek yapılabilir. Bu ayarlar, [Web çalışanları Ile ındexdb 'yi destekleyen tarayıcılara](https://caniuse.com/#feat=indexeddb)kaydedilir.

> [!NOTE]
> `headers` `parameters` `body` `url` Tüm çalışma kitabı kullanıcıları tarafından görülebilmesi için alanlardan herhangi bir gizli dizi (,,,) eklemeyin.

Bu sağlayıcı [Jsonpath](workbooks-jsonpath.md)'i destekliyor.

## <a name="next-steps"></a>Sonraki adımlar

* Çok sayıda zengin görselleştirmeler seçeneği hakkında daha fazla [bilgi edinmeye başlayın](./workbooks-overview.md#visualizations) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](./workbooks-access-control.md) ve paylaşma.
* [Log Analytics sorgu iyileştirme ipuçları](../logs/query-optimization.md)
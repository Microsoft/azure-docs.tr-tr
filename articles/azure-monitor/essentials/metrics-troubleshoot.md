---
title: Azure Izleyici ölçüm grafiklerde sorun giderme
description: Ölçüm grafiklerini oluşturma, özelleştirme veya yorumlama sorunlarını giderme
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.openlocfilehash: 6acab9618566766b12b4b236c75aa58386100513
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961073"
---
# <a name="troubleshooting-metrics-charts"></a>Ölçüm grafikleriyle ilgili sorunları giderme

Azure Ölçüm Gezgini 'nde grafik oluşturma, özelleştirme veya yorumlama ile ilgili sorunlar yaşıyorsanız bu makaleyi kullanın. Ölçümlerle ilgili yeni [başladıysanız](metrics-getting-started.md) Ölçüm Gezgini ' [nin ve gelişmiş özellikler](../essentials/metrics-charts.md)' i kullanmaya başlama hakkında bilgi edinin. Ayrıca, yapılandırılmış ölçüm grafiklerinin [örneklerini](../essentials/metric-chart-samples.md) de görebilirsiniz.

## <a name="chart-shows-no-data"></a>Grafik veri göstermez

Bazı durumlarda, doğru kaynakları ve ölçümleri seçtikten sonra grafiklerde veri gösterilmeyebilir. Bu davranışa aşağıdakilerden biri neden olabilir:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Aboneliğiniz için Microsoft.Insights kaynak sağlayıcısı kaydedilmedi

Ölçümlerin incelenmesi için aboneliğinizde *Microsoft.Insights* kaynak sağlayıcısının kaydedilmesi gerekir. Çoğu durumda otomatik olarak kaydedilir (başka bir deyişle siz bir uyarı kuralını yapılandırdıktan, herhangi bir kaynak için tanılama ayarlarını özelleştirdikten veya otomatik ölçeklendirme kuralı yapılandırdıktan sonra). Microsoft. Insights kaynak sağlayıcısı kayıtlı değilse, [Azure kaynak sağlayıcıları ve türleri](../../azure-resource-manager/management/resource-providers-and-types.md)bölümünde açıklanan adımları izleyerek el ile kaydetmeniz gerekir.

**Çözüm:** **Abonelikler**, **kaynak sağlayıcıları** sekmesini açın ve aboneliğiniz için *Microsoft. Insights* 'ın kaydedildiğini doğrulayın.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Kaynağınız üzerinde yeterli erişim haklarınız yok

Azure 'da ölçümlere erişim, [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)tarafından denetlenir. Herhangi bir kaynağın ölçümlerini incelemek için [izleme okuyucusu](../../role-based-access-control/built-in-roles.md#monitoring-reader), [izleme katkıda bulunanı](../../role-based-access-control/built-in-roles.md#monitoring-contributor) veya [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) üyesi olmalısınız.

**Çözüm:** Ölçümleri araştırırken kaynak için yeterli izinlere sahip olduğunuzdan emin olun.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Kaynağınız seçilen zaman aralığı boyunca ölçüm göstermedi

Bazı kaynaklar ölçümlerini sürekli göstermez. Örneğin Azure durdurulan sanal makineler için ölçüm toplamayacaktır. Diğer kaynaklar ancak bazı koşullar oluştuğunda ölçümlerini gösterebilir. Örneğin işlemin işleme süresini gösteren ölçüm için en az bir işlem gerekir. Seçilen zaman aralığı içinde işlem yoksa grafik doğal olarak boş olacaktır. Bunun yanı sıra Azure'daki ölçümlerin çoğu her dakika toplansa da, bazıları daha seyrek toplanır. İncelemeye çalıştığınız ölçüm hakkındaki diğer ayrıntıları almak için ölçüm belgelerine bakın.

**Çözüm:** Grafiğin saatini daha geniş bir aralığa çevirin. Daha büyük bir zaman ayrıntı düzeyi kullanarak "son 30 gün" ile başlayabilir (veya "otomatik zaman ayrıntı düzeyi" seçeneğine bağlı olarak).

### <a name="you-picked-a-time-range-greater-than-30-days"></a>30 günden uzun bir zaman aralığı seçtiniz

[Azure 'daki ölçümlerin çoğu 93 gün boyunca depolanır](../essentials/data-platform-metrics.md#retention-of-metrics). Bununla birlikte tek grafikte en çok 30 günlük verileri sorgulayabilirsiniz. Bu sınırlama [günlük tabanlı ölçümler](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) için geçerli değildir.

**Çözüm:** Boş bir grafik görürseniz veya grafiğiniz yalnızca ölçüm verilerinin bir kısmını görüntülüyorsa, saat seçicideki başlangıç ve bitiş tarihleri arasındaki farkın 30 günlük aralığı aşmadığını doğrulayın. 30 günlük bir Aralık seçtikten sonra, tam saklama penceresini görüntülemek için grafiği [kaydırabilirsiniz](metrics-charts.md#pan) .

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Tüm ölçüm değerleri kilitli y ekseni aralığının dışındaydı

[Grafiğin y ekseninin sınırlarını kilitleyerek](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis) istemeden grafik görüntüleme alanının grafik çizgisini göstermemesini sağlayabilirsiniz. Örneğin y ekseni %0 ile %50 arasına kilitlendiyse ve ölçümün %100 olan bir sabit değeri varsa, çizgi her zaman görünür alanın dışında işlenir ve grafik boş görünür.

**Çözüm:** Grafiğin y ekseni sınırlarının, ölçüm değerleri aralığının dışında kilitlenmediğini doğrulayın. Y ekseni sınırları kilitlendiyse, ölçüm değerlerinin grafik aralığının dışında kalmaması için bunları geçici olarak sıfırlamak isteyebilirsiniz. **Sum**, **min** ve **max** toplaması olan grafiklerde y ekseni aralığının otomatik ayrıntı düzeyiyle kilitlenmesi önerilmez çünkü tarayıcı penceresi yeniden boyutlandırıldığında veya bir ekran çözünürlüğünden diğerine geçildiğinde bunların değerleri ayrıntı düzeyiyle birlikte değişir. Ayrıntı düzeyinde geçiş yapmak grafiğinizin görüntü alanını boş bırakabilir.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Konuk işletim sistemi ölçüsüne bakıyorsunuz, ancak Azure tanılama uzantısı 'nı etkinleştirmediniz

**Konuk İşletim Sistemi** ölçümleri koleksiyonu Azure Tanılama Uzantısı'nın yapılandırılmasını veya kaynağınız için **Tanılama Ayarları** paneli kullanılarak etkinleştirilmesini gerektirir.

**Çözüm:** Azure Tanılama uzantısı etkinse ancak ölçümlerinizi göremiyorsanız, [Azure tanılama uzantısı sorun giderme kılavuzunda](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)açıklanan adımları izleyin. Ayrıca bkz. sorun giderme adımları için [Konuk işletim sistemi ad alanı ve ölçümleri](#cannot-pick-guest-os-namespace-and-metrics) seçemezsiniz

## <a name="error-retrieving-data-message-on-dashboard"></a>Panoda "verileri alma hatası" iletisi

Panonuz daha sonra kullanım dışı bırakılan ve Azure'dan kaldırılan bir ölçümle oluşturulduğunda bu hata oluşabilir. Durum olduğunu doğrulamak için kaynağınızın **ölçümler** sekmesini açın ve ölçüm seçicideki kullanılabilir ölçümleri denetleyin. Ölçüm gösterilmiyorsa Azure'dan kaldırılmış demektir. Genellikle bir ölçüm kullanım dışı bırakıldığında kaynak durumuyla ilgili benzer bir perspektif sağlayan yeni ve daha iyi bir ölçüm vardır.

**Çözüm:** Panodaki grafik için alternatif bir ölçüm seçerek başarısız kutucuğu güncelleştirin. [Azure hizmetleri için kullanılabilir ölçümlerin listesini gözden geçirebilirsiniz](./metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Grafik kesikli çizgi gösteriyor

Azure ölçüm grafikleri, iki bilinen zaman çizgisi veri noktası arasında eksik bir değer ("null değer" olarak da bilinir) olduğunu göstermek için kesikli çizgi stilini kullanır. Örneğin, "1 dakikalık" zaman ayrıntı düzeyi olarak seçtiğiniz zaman seçici, ancak ölçüm 07:26, 07:27, 07:29 ve 07:30 (ikinci ve üçüncü veri noktaları arasında bir dakika boşluğu) ile bildirilmişse, kesikli bir çizgi, 07:27 ve 07:29 bağlanır ve düz bir çizgi diğer tüm veri noktalarını birbirine bağlayacaktır. Ölçüm **sayı** ve **Toplam** toplama kullandığında kesikli çizgi sıfıra düşer. **Ortalama**, **En düşük** veya **en büyük** toplamalar için, kesikli çizgi en yakın bilinen iki veri noktasını bağlar. Ayrıca grafiğin en sağ veya en sol ucunda veri eksik olduğunda, kesikli çizgi eksik veri noktasının yönünde genişletilir.
  ![Grafiğin sağ veya sol tarafında verilerin eksik olduğunu gösteren ekran görüntüsü, kesikli çizgi, eksik veri noktasının yönüne genişletilir.](./media/metrics-troubleshoot/dashed-line.png)

**Çözüm:** Bu davranış tasarıma göre yapılır. Eksik veri noktalarını belirlemek için kullanışlıdır. Çizgi grafik, yüksek yoğunluklu ölçümlerin eğilimlerini görselleştirmeye yönelik bir üst seçimdir, ancak özellikle de zaman dilimi ile birlikte bulunan değerler önemli olduğunda, seyrek değerlerle ölçümleri yorumlamak zor olabilir. Kesikli çizgi bu grafiklerin okunmasını kolaylaştırabilir ama grafiğiniz yine de belirsiz görünüyorsa ölçümlerinizi farklı bir grafik türünde görüntülemeyi göz önünde bulundurun. Örneğin, aynı ölçüm için dağınık bir çizim grafiği, yalnızca bir değer olduğunda bir noktayı görselleştirerek ve değer eksik olduğunda veri noktasını tamamen atlayarak her seferinde her zaman Grey gösterir: ![ dağılım grafiği menü seçeneğini vurgulayan ekran görüntüsü.](./media/metrics-troubleshoot/scatter-plot.png)

   > [!NOTE]
   > Ölçümünüz için yine de çizgi grafiği tercih ediyorsanız, fareyi grafiğin üzerinde hareket ettirmek zaman dilimini değerlendirmenize yardımcı olabilir çünkü fare işaretçisinin bulunduğu konumdaki veri noktası vurgulanır.

## <a name="chart-shows-unexpected-drop-in-values"></a>Grafik değerlerde beklenmedik bir düşüş olduğunu gösteriyor

Çoğu durumda ölçüm değerlerinde algılanan düşüş grafikte gösterilen verilerin yanlış anlaşılmasından kaynaklanır. Son ölçüm verileri Azure tarafından henüz alınmadığı veya işlenmediği için grafikte en yakın dakikalar gösterildiğinde toplamlardaki veya sayılardaki bir düşüş sizi yanıltabilir. Hizmete bağlı olarak ölçümlerin işlenmesindeki gecikme süresi birkaç dakikalık bir süre olabilir. 1 veya 5 dakikalık ayrıntı düzeyi içeren son zaman aralığını gösteren grafiklerde, son birkaç dakika içinde değerin bir tanesi daha belirgin hale gelir: ![ son birkaç dakika içinde değerin bir bölümünü gösteren ekran görüntüsü.](./media/metrics-troubleshoot/unexpected-dip.png)

**Çözüm:** Bu davranış tasarıma göre yapılır. Veriler *kısmi* veya *eksik* bile olsa, verileri aldığımız anda göstermenin yararlı olduğuna inanıyoruz. Bu sayede önemli sonuçlara daha çabuk varabilir ve araştırmayı hemen başlatabilirsiniz. Örneğin, hata sayısını gösteren bir ölçüm için kısmı X değerinin gösterilmesi, belirli bir dakikada en az X hata olduğunu anlamanızı sağlar. Söz konusu dakikada gerçekleşen tam hata sayısını (üstelik bu sayı o kadar da önemli olmayabilir) görmek için beklemek yerine sorunu araştırmaya hemen başlayabilirsiniz. Veri kümesinin tamamını aldığımızda grafik güncelleştirilir ama o zaman da daha yakın dakikalarda elde edilen yeni eksik veri noktaları gösterilebilir.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Konuk işletim sistemi ad alanı ve ölçümleri seçemezsiniz

Sanal makinelerin ve sanal makine ölçek kümelerinin ölçümleri iki kategoriye ayrılır: Azure barındırma ortamı tarafından toplanan **Sanal Makine Konağı** ölçümleri ve sanal makinelerinizde çalıştırılan [izleme aracısı](../agents/agents-overview.md) tarafından toplanan **Konuk İşletim Sistemi (klasik)** ölçümleri. İzleme aracısını, [Azure Tanılama Uzantısı](../agents/diagnostics-extension-overview.md)'nı etkinleştirerek yüklersiniz.

Varsayılan olarak Konuk İşletim Sistemi ölçümleri, kaynağınızın **Tanılama ayarları** sekmesinde seçtiğiniz Azure Depolama hesabında depolanır. Konuk İşletim Sistemi ölçümleri toplanmıyorsa veya ölçüm gezgini bunlara erişemiyorsa yalnızca **Sanal Makine Konağı** ölçüm ad alanını görürsünüz:

![ölçüm resmi](./media/metrics-troubleshoot/vm.png)

**Çözüm:** Bir **Konuk işletim sistemi (klasik)** ad alanını ve Ölçüm Gezgini 'nde ölçümleri görmüyorsanız:

1. [Azure Tanılama Uzantısı](../agents/diagnostics-extension-overview.md)'nın etkinleştirildiğini ve ölçümleri toplamak için yapılandırıldığını onaylayın.
    > [!WARNING]
    > **Konuk İşletim Sistemi**'ni depolama hesabına göndermek için [Log Analytics aracısını](../agents/agents-overview.md#log-analytics-agent) (Microsoft İzleyici Aracısı veya "MMA" olarak da adlandırılır) kullanamazsınız.

1. [Aboneliğiniz Için](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription) **Microsoft. Insights** kaynak sağlayıcısının kayıtlı olduğundan emin olun.

1. Depolama hesabının güvenlik duvarı tarafından korunmadığını doğrulayın. Azure portalının ölçüm verilerini almak ve grafikleri çizmek için depolama hesabına erişmesi gerekiyor.

1. Ölçümlerin depolama hesabına akmasını doğrulamak için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) kullanın. Ölçümler toplanmıyorsa [Azure Tanılama Uzantısı sorun giderme kılavuzunu](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal) izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Ölçüm Gezgini 'ni kullanmaya başlama hakkında bilgi edinin](metrics-getting-started.md)
* [Ölçüm Gezgini 'nin gelişmiş özellikleri hakkında bilgi edinin](../essentials/metrics-charts.md)
* [Azure hizmetleri için kullanılabilir ölçümlerin listesine bakın](./metrics-supported.md)
* [Yapılandırılmış grafik örneklerine bakın](../essentials/metric-chart-samples.md)
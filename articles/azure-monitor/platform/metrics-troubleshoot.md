---
title: Azure Izleyici ölçüm grafiklerde sorun giderme
description: Ölçüm grafiklerini oluşturma, özelleştirme veya yorumlama sorunlarını giderme
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: d31b046bf02893affff84069ee92b3bd7735b904
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243230"
---
# <a name="troubleshooting-metrics-charts"></a>Ölçüm grafiklerini sorunlarını giderme

Azure Ölçüm Gezgini 'nde grafik oluşturma, özelleştirme veya yorumlama ile ilgili sorunlar yaşıyorsanız bu makaleyi kullanın. Ölçümlerle ilgili yeni [başladıysanız](metrics-getting-started.md) Ölçüm Gezgini ' [nin ve gelişmiş özellikler](metrics-charts.md)' i kullanmaya başlama hakkında bilgi edinin. Ayrıca, yapılandırılmış ölçüm grafiklerinin [örneklerini](metric-chart-samples.md) de görebilirsiniz.

## <a name="cant-find-your-resource-to-select-it"></a>Kaynağı seçmek için bulunamıyor

Kaynak **Seç** düğmesine tıkladınız, ancak kaynağı kaynak Seçicisi iletişim kutusunda görmezsiniz.

**Çözüm:** Ölçüm Gezgini kullanılabilir kaynakları listelemesi için önce abonelikleri ve kaynak gruplarını seçmenizi gerektirir. Kaynağı görmüyorsanız:

1. **Abonelik** açılan menüsünde doğru aboneliği seçtiğinizden emin olun. Aboneliğiniz listelenmiyorsa, **Dizin + abonelik ayarları** ' na tıklayın ve kaynağınızın bulunduğu bir abonelik ekleyin.

1. Doğru kaynak grubunu seçtiğinizden emin olun.
    > [!WARNING]
    > En iyi performans için, Ölçüm Gezgini 'ni ilk açışınızda **kaynak grubu** açılır listesinde önceden seçilmiş kaynak grupları yoktur. Herhangi bir kaynağı görebilmeniz için en az bir grup seçmeniz gerekir.

## <a name="chart-shows-no-data"></a>Grafik veri göstermez

Bazı durumlarda, doğru kaynakları ve ölçümleri seçtikten sonra grafiklerde veri gösterilmeyebilir. Bu davranışa aşağıdakilerden biri neden olabilir:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Aboneliğiniz için Microsoft. Insights kaynak sağlayıcısı kayıtlı değil

Ölçümleri keşfetmek için aboneliğinizde kayıtlı *Microsoft. Insights* kaynak sağlayıcısı gerekir. Çoğu durumda, otomatik olarak kaydedilir (yani, bir uyarı kuralını yapılandırdıktan sonra, herhangi bir kaynak için tanılama ayarlarını özelleştirebilir veya bir otomatik ölçeklendirme kuralı yapılandırırsınız). Microsoft. Insights kaynak sağlayıcısı kayıtlı değilse, [Azure kaynak sağlayıcıları ve türleri](../../azure-resource-manager/resource-manager-supported-services.md)bölümünde açıklanan adımları izleyerek el ile kaydetmeniz gerekir.

**Çözüm:** **Abonelikler**, **kaynak sağlayıcıları** sekmesini açın ve aboneliğiniz için *Microsoft. Insights* 'ın kaydedildiğini doğrulayın.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Kaynağınız için yeterli erişim haklarınız yok

Azure 'da ölçümlere erişim [rol tabanlı erişim denetimi (RBAC)](../../role-based-access-control/overview.md)tarafından denetlenir. Herhangi bir kaynakla ilgili ölçümleri araştırmak için, [izleme okuyucusu](../../role-based-access-control/built-in-roles.md#monitoring-reader), [katkıda bulunan izleme](../../role-based-access-control/built-in-roles.md#monitoring-contributor)veya [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) bir üye olmanız gerekir.

**Çözüm:** Ölçümleri araştırırken kaynak için yeterli izinlere sahip olduğunuzdan emin olun.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Kaynağınız, seçili zaman aralığı sırasında ölçümleri yaymadı

Bazı kaynaklar ölçümleri sürekli olarak göstermiyor. Örneğin, Azure durdurulmuş sanal makineler için ölçümler toplamaz. Diğer kaynaklar, yalnızca bir koşul gerçekleştiğinde ölçümlerini gösterebilir. Örneğin, bir işlemin işlem süresini gösteren bir ölçüm en az bir işlem gerektirir. Seçilen zaman aralığında hiçbir işlem yoksa grafik doğal olarak boş olur. Ayrıca, Azure 'daki ölçümlerin büyük bölümü her dakika toplanırken, daha az miktarda toplanan bazı bazıları vardır. Keşfetmeye çalıştığınız ölçüm hakkında daha fazla ayrıntı edinmek için ölçüm belgelerine bakın.

**Çözüm:** Grafiğin saatini daha geniş bir aralığa çevirin. Daha büyük bir zaman ayrıntı düzeyi kullanarak "son 30 gün" ile başlayabilir (veya "otomatik zaman ayrıntı düzeyi" seçeneğine bağlı olarak).

### <a name="you-picked-a-time-range-greater-than-30-days"></a>30 günden daha uzun bir zaman aralığı seçtiniz

[Azure 'daki ölçümlerin çoğu 93 gün boyunca depolanır](data-platform-metrics.md#retention-of-metrics). Ancak, yalnızca herhangi bir grafik üzerinde en fazla 30 günden fazla veri için sorgulama yapabilirsiniz. Bu sınırlama, [günlük tabanlı ölçümler](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)için geçerlidir.

**Çözüm:** Boş bir grafik görürseniz veya grafiğiniz yalnızca ölçüm verilerinin bir kısmını görüntülüyorsa, saat seçicideki başlangıç ve bitiş tarihleri arasındaki farkın 30 günlük aralığı aşmadığını doğrulayın.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Tüm ölçüm değerleri kilitli y ekseni aralığının dışındaydı

Grafik [y ekseninin sınırlarını kilitleyerek](metrics-charts.md#lock-boundaries-of-chart-y-axis)grafik görüntüleme alanında grafik çizgisini gösterme istenmeden bir şekilde ayarlayabilirsiniz. Örneğin, y ekseni% 0 ile% 50 arasında bir aralığa kilitliyse ve ölçümün% 100 sabit değeri varsa, satır her zaman görünür alanın dışında işlenir ve grafik boş görünür.

**Çözüm:** Grafiğin y ekseni sınırlarının, ölçüm değerleri aralığının dışında kilitlenmediğini doğrulayın. Y ekseni sınırları kilitliyse, ölçüm değerlerinin grafik aralığının dışına düşmemesini sağlamak için bunları geçici olarak sıfırlamak isteyebilirsiniz. **Toplam**, **En düşük**ve **en fazla** toplama özelliklerine sahip grafikler için otomatik ayrıntı düzeyi kullanılması önerilmez, çünkü değerleri tarayıcı penceresini yeniden boyutlandırabilir veya bir ekran çözünürlüğünden geçin başka bir. Ayrıntı düzeyi değiştirme, grafiğinizin görüntü alanının boş kalmasını sağlayabilir.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Konuk işletim sistemi ölçüsüne bakıyorsunuz, ancak Azure tanılama uzantısı 'nı etkinleştirmediniz

**Konuk işletim sistemi** ölçümleri koleksiyonu, Azure tanılama uzantısının yapılandırılmasını veya kaynağınızın **Tanılama ayarları** paneli kullanılarak etkinleştirilmesini gerektirir.

**Çözüm:** Azure Tanılama uzantısı etkinse ancak ölçümlerinizi göremiyorsanız, [Azure tanılama uzantısı sorun giderme kılavuzunda](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)açıklanan adımları izleyin. Ayrıca bkz. sorun giderme adımları için [Konuk işletim sistemi ad alanı ve ölçümleri](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics) seçemezsiniz

## <a name="error-retrieving-data-message-on-dashboard"></a>Panoda "verileri alma hatası" iletisi

Bu sorun, panonuz daha sonra kullanım dışı bırakılmış ve Azure 'dan kaldırılan bir ölçümle oluşturulduğunda gerçekleşebilir. Durum olduğunu doğrulamak için kaynağınızın **ölçümler** sekmesini açın ve ölçüm seçicideki kullanılabilir ölçümleri denetleyin. Ölçüm gösterilmezse, ölçüm Azure 'dan kaldırılmıştır. Genellikle, bir ölçüm kullanım dışı olduğunda, kaynak sistem durumu üzerinde benzer bir perspektifle birlikte sağlayan daha yeni bir ölçüm vardır.

**Çözüm:** Panodaki grafik için alternatif bir ölçüm seçerek başarısız kutucuğu güncelleştirin. [Azure hizmetleri için kullanılabilir ölçümlerin listesini gözden](metrics-supported.md)geçirebilirsiniz.

## <a name="chart-shows-dashed-line"></a>Grafik kesikli çizgiyi gösterir

Azure ölçüm grafikleri, iki bilinen zaman çizgisi veri noktası arasında eksik bir değer ("null değer" olarak da bilinir) olduğunu göstermek için kesikli çizgi stilini kullanır. Örneğin, saat seçicide "1 dakika" zaman ayrıntı düzeyi aldıysanız ancak ölçüm 07:26, 07:27, 07:29 ve 07:30 (ikinci ve üçüncü veri noktaları arasında bir dakika boşluğu) ile bildirilmişse, kesikli bir çizgi, 07:27 ve 07:29 bağlanır ve düz bir çizgi bağlanır Tüm diğer veri noktaları. Ölçüm **sayı** ve **Toplam** toplama kullandığında kesikli çizgi sıfıra düşer. **Ortalama**, **En düşük** veya **en büyük** toplamalar için, kesikli çizgi en yakın bilinen iki veri noktasını bağlar. Ayrıca, grafiğin sağ veya sol tarafındaki veriler eksik olduğunda kesikli çizgi, eksik veri noktasının yönüne genişletilir.
  ![ölçüm resmi @ no__t-1

**Çözüm:** Bu davranış tasarıma göre yapılır. Eksik veri noktalarını belirlemek için faydalıdır. Çizgi grafik, yüksek yoğunluklu ölçümlerin eğilimlerini görselleştirmeye yönelik bir üst seçimdir, ancak özellikle de zaman dilimi ile birlikte bulunan değerler önemli olduğunda, seyrek değerlerle ölçümleri yorumlamak zor olabilir. Kesik çizgi satırı Bu grafiklerin okunmasını kolaylaştırır, ancak grafiğiniz hala net değilse, ölçümlerinizi farklı bir grafik türüyle görüntülemeyi düşünün. Örneğin, aynı ölçüm için dağınık bir çizim grafiği, yalnızca bir değer olduğunda bir noktayı görselleştirerek ve değer eksik olduğunda veri noktasını tamamen atlayarak, her zaman Grey 'i açıkça gösterir: ![metric Image @ no__t-1

   > [!NOTE]
   > Ölçüm için bir çizgi grafik tercih ediyorsanız, fareyi grafiğe taşımak, fare işaretçisinin konumundaki veri noktasını vurgulayarak zaman parçalı yapısını değerlendirmenize yardımcı olabilir.

## <a name="chart-shows-unexpected-drop-in-values"></a>Grafik, beklenmeyen değerleri gösterir

Birçok durumda, ölçüm değerlerindeki algılanan bırakma grafikte gösterilen verilerin yanlış anlaşılmasıdır. Son ölçüm veri noktaları henüz Azure tarafından alınamadığından veya işlenemediği için, bir veya daha fazla dakika içinde bir bırakma veya sayımlar halinde bir bırakma ile yanlış olabilir. Hizmete bağlı olarak, ölçüm işleme gecikmesi birkaç dakika aralığında olabilir. 1 veya 5 dakikalık ayrıntı düzeyi içeren son zaman aralığını gösteren grafiklerde, son birkaç dakika içinde değerin bir tanesi daha belirgin hale gelir: ![metrik görüntü @ no__t-1

**Çözüm:** Bu davranış tasarıma göre yapılır. Verilerin *kısmen* veya *eksik*olduğu durumlarda bile yararlı olduğunu düşündükten sonra verileri göstermemiz gerektiğine inandık. Bunun yapılması, önemli bir sonucu daha erken yapmanıza ve araştırmanın hemen başlamasını sağlar. Örneğin, bir kısmi değer gösteren bir ölçüm için, belirli bir dakika boyunca en az X başarısızlık olduğunu bildiren bir X. Bu dakikada gerçekleşen hata sayısını tam olarak görmek için beklemek yerine sorunu hemen incelemeye başlayabilir. Bu, önemli olmayan bir durum olabilir. Tüm veri kümesini aldığımızda grafik güncelleştirilecek, ancak bu süre içinde, daha son dakikadan daha yeni tamamlanmamış veri noktaları da gösterilebilir.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Konuk işletim sistemi ad alanı ve ölçümleri seçemezsiniz

Sanal makineler ve sanal makine ölçek kümelerinin iki ölçüm kategorisi vardır: Azure barındırma ortamı tarafından toplanan **sanal makine konak** ölçümleri ve izleme Aracısı tarafından toplanan **Konuk işletim sistemi (klasik)** ölçümleri [ ](agents-overview.md)sanal makinelerinizde çalışıyor. İzleme aracısını [Azure tanılama uzantısı](diagnostics-extension-overview.md)' nı etkinleştirerek yüklersiniz.

Varsayılan olarak, Konuk işletim sistemi ölçümleri, kaynağınızın **Tanılama ayarları** sekmesinden seçtiğiniz Azure depolama hesabında depolanır. Konuk işletim sistemi ölçümleri toplanmıyorsa veya ölçüm Gezgini bunlara erişemez ise yalnızca **sanal makine konak** ölçümü ad alanını görürsünüz:

![ölçüm resmi](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Çözüm:** Bir **Konuk işletim sistemi (klasik)** ad alanını ve Ölçüm Gezgini 'nde ölçümleri görmüyorsanız:

1. [Azure tanılama uzantısının](diagnostics-extension-overview.md) etkinleştirildiğini ve ölçümleri toplamak üzere yapılandırıldığını doğrulayın.
    > [!WARNING]
    > **Konuk işletim sistemini** bir depolama hesabına göndermek için [Log Analytics Aracısı](agents-overview.md#log-analytics-agent) (Microsoft Monitoring Agent veya "MMA" olarak da bilinir) kullanamazsınız.

1. [Aboneliğiniz Için](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription) **Microsoft. Insights** kaynak sağlayıcısının kayıtlı olduğundan emin olun.

1. Depolama hesabının güvenlik duvarı tarafından korunmadığından emin olun. Azure portal, ölçüm verilerinin alınması ve grafiklerin çizilmesi için depolama hesabına erişmesi gerekir.

1. Ölçümlerin depolama hesabına akmasını doğrulamak için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) 'ni kullanın. Ölçümler toplanmıyorsa [Azure tanılama uzantısı sorun giderme kılavuzunu](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Ölçüm Gezgini 'ni kullanmaya başlama hakkında bilgi edinin](metrics-getting-started.md)
* [Ölçüm Gezgini 'nin gelişmiş özellikleri hakkında bilgi edinin](metrics-charts.md)
* [Azure hizmetleri için kullanılabilir ölçümlerin listesini görüntüleyin](metrics-supported.md)
* [Bkz. yapılandırılan grafik örnekleri](metric-chart-samples.md)

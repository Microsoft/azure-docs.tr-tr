---
title: Azure Izleyici 'de izleme & birleştirilmiş uyarı, klasik uyarı & izlemeyi değiştirir
description: Daha önce uyarılar (klasik) altında Azure portal gösterildiği gibi, klasik izleme hizmetleri ve işlevlerinin kullanımdan kaldırılması hakkında genel bakış. Klasik uyarı & izleme, Azure kaynakları için klasik ölçüm uyarılarını, Application Insights için klasik ölçüm uyarılarını, Application Insights ve klasik özel ölçüm tabanlı uyarılar Application Insights için klasik özel ölçüm uyarılarını içerir Application Insights SmartDetection v1 uyarıları
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: ea50a4f10aa1d794f875cb1eb9601774bec146cd
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676889"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Azure Izleyici 'de izleme & birleştirilmiş uyarı, klasik uyarı & izlemeyi değiştirir

Azure Izleyici artık kaynakların tamamında ' tek ölçüm ' ve ' bir uyarı ' desteği sunan birleştirilmiş bir tam yığın izleme hizmeti haline geldi; daha fazla bilgi için [yeni Azure izleyici 'de blog gönderimize](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)bakın. Yeni Azure izleme ve uyarı platformları daha hızlı, daha akıllı ve genişletilebilir olacak şekilde geliştirilmiştir; bulut bilgi işlem ve Microsoft Akıllı bulut felseonuna göre daha fazla bilgi almak üzere tasarlanmıştır. 

Yeni Azure izleme ve uyarı platformu sayesinde, Azure uyarıları 'nın *Klasik uyarılarını görüntüle* bölümünde barındırılan "klasik" izleme ve uyarı platformunu devre dışı bırakılacağız, **Azure genel bulutlarında Ağustos 2019 tarafından kullanım dışı** bırakılacak . [Azure Kamu Bulutu](../../azure-government/documentation-government-welcome.md) ve [Azure Çin 21Vianet](https://docs.azure.cn/) etkilenmeyecektir.

> [!NOTE]
> Geçiş Aracı 'nın çıkış gecikmesi nedeniyle, klasik uyarılar geçişinin devre dışı bırakılması tarihi 31 Haziran 2019 tarihinde ilk bildirilen tarihten itibaren [31 ağustos 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) tarihine kadar genişletilmiştir.

 ![Azure portal 'de klasik uyarı](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Başlamanızı ve yeni platformda uyarılarınızı yeniden oluşturmayı öneririz. Çok sayıda uyarı olan müşteriler için, var olan klasik uyarıları, kesintiye uğramadan veya ek maliyetler olmadan yeni uyarılar sistemine taşımaya yönelik [gönüllü bir geçiş aracı](alerts-using-migration-tool.md) olan [aşamalar halinde kullanıma](alerts-understand-migration.md#rollout-phases)sunuyoruz.

> [!IMPORTANT]
> Etkinlik günlüğünde oluşturulan klasik uyarı kuralları kullanım dışı olmayacaktır veya geçirilmez. Etkinlik günlüğünde oluşturulan tüm klasik uyarı kuralları, yeni Azure Izleyici-uyarılarından olduğu gibi erişilebilir ve kullanılabilir. Daha fazla bilgi için bkz. [Azure izleyici kullanarak etkinlik günlüğü uyarılarını oluşturma, görüntüleme ve yönetme](../../azure-monitor/platform/alerts-activity-log.md). Benzer şekilde, hizmet durumundaki uyarılara erişilebilir ve yeni hizmet durumu bölümünde olduğu gibi kullanılabilirler. Ayrıntılar için bkz. [hizmet durumu bildirimleri uyarıları](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Application Insights 'daki birleştirilmiş ölçümler ve uyarılar

Azure Izleyici 'nin daha yeni ölçüm platformu, şimdi Application Insights 'tan geliyor. Bu taşıma Işlemi, yalnızca önceki e-posta ve Web kancası çağrılarından çok daha fazla seçeneğe izin veren Application Insights eylem gruplarına kanca anlamına gelir. Uyarılar artık ServiceNow ve Automation runbook 'Ları gibi sesli aramalar, Azure Işlevleri, Logic Apps, SMS ve ıTSM araçları tetiklenebilir. Neredeyse gerçek zamanlı izleme ve uyarı sayesinde yeni platform, Application Insights kullanıcıların diğer Azure kaynakları genelinde izlemeyi ve Microsoft ürünlerinin izlenmesini yeniden dağıtmalarını sağlar.

Application Insights için yeni Birleşik Izleme ve uyarı şu şekilde alınacaktır:

- **Application Insights platform ölçümleri** : Application Insights üründen popüler ön derlenmiş ölçümler sağlar. Daha fazla bilgi için, [yeni Azure izleyici Application Insights Için platform ölçümlerini](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)kullanma hakkında bu makaleye bakın.
- **Application Insights kullanılabilirliği ve Web testi** , Web uygulamanızın veya sunucunuzun yanıt hızını ve kullanılabilirliğini değerlendirmenizi sağlar. Daha fazla bilgi için, [yeni Azure izleyici Application Insights kullanılabilirlik testlerini ve uyarılarını](../../azure-monitor/app/monitor-web-app-availability.md)kullanma hakkında bu makaleye bakın.
- İzleme ve uyarılar için kendi ölçümlerini tanımlamanızı ve yaymanızı sağlayan **özel ölçümler Application Insights** . Daha fazla bilgi için, [yeni Azure izleyici Application Insights Için özel ölçüm](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)kullanma hakkında bu makaleye bakın.
- **Application Insights hata bozukluileri (akıllı algılama 'nın parçası)** : Web UYGULAMANıZ başarısız http istekleri veya bağımlılık çağrıları ücretlerinde anormal bir artış yaşıyorsa sizi gerçek zamanlı olarak neredeyse gerçek zamanlı olarak bildirir. Daha fazla bilgi için [akıllı algılama-hata Anormallarını](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics)kullanma hakkında bu makaleye bakın.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Diğer Azure kaynakları için birleştirilmiş ölçümler ve uyarılar

Mart 2018 ' den itibaren Azure kaynakları için bir sonraki nesil uyarı ve çok boyutlu izleme kullanılabilirliği vardır. Artık yeni ölçüm platformu ve uyarı, neredeyse gerçek zamanlı yetenekler sayesinde daha hızlıdır. Daha da önemlisi, daha yeni platform, belirli bir değer birleşimine, koşula veya işleme dilimlemenize ve filtrelemenize olanak sağlayan boyutlar seçeneğini içerdiği için daha yeni ölçüm platformu uyarıları daha fazla ayrıntı sağlar. Yeni Azure Izleyici 'deki tüm uyarılar gibi, daha yeni ölçüm uyarıları ActionGroups kullanımı ile daha geniştir. bildirimler, e-posta veya Web kancasının ötesinde SMS, Voice, Azure Işlevi, Otomasyon Runbook 'U ve daha fazlasını genişletmeye olanak tanır. Daha fazla bilgi için bkz. [Azure izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../../azure-monitor/platform/alerts-metric.md).
Azure kaynakları için yeni ölçümler şu şekilde kullanılabilir:

- **Azure Izleyici standart platform ölçümleri** : çeşitli Azure hizmetlerinden ve ürünlerinden daha önceden doldurulan popüler ölçümler sağlar. Daha fazla bilgi için [Azure izleyici 'de desteklenen ölçümler](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) ve [Azure izleyici 'de ölçüm uyarılarını destekleme](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts)hakkında bu makaleye bakın.
- **Azure Izleyici özel ölçümleri** : Azure tanılama Aracısı da dahil olmak üzere Kullanıcı tarafından yönetilen kaynaklardan ölçümler sağlar. Daha fazla bilgi için bkz. [Azure izleyici 'de özel ölçümler](../../azure-monitor/platform/metrics-custom-overview.md)hakkında bu makaleye bakın. Özel ölçümleri kullanarak, [Windows Azure tanılama Aracısı](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) ve [etkileyen telegraf Aracısı](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)tarafından toplanan ölçümleri de yayımlayabilirsiniz.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Klasik izleme ve uyarı platformunun kullanımdan kaldırılması

Daha önce belirtildiği gibi, şu anda Azure portal [Uyarılar (klasik) bölümünden](../../azure-monitor/platform/alerts-classic.overview.md) kullanılabilir olan klasik izleme ve uyarı platformu, daha yeni sistemle değiştirildikleri için gelecek aylarda kullanımdan kaldırılacaktır.
Daha eski klasik izleme ve uyarı, 31 Ağustos 2019 tarihinde kullanımdan kaldırılacaktır. ilgili API 'lerin kapatılmasını, Azure portal arabirimi ve hizmetleri de dahil olmak üzere. Özellikle, bu özellikler kullanım dışı olacaktır:

- Azure kaynakları için daha eski (klasik) ölçümler ve uyarılar Azure portal, [Uyarılar (klasik) bölümünde](../../azure-monitor/platform/alerts-classic.overview.md) Şu anda kullanılabilir; [Microsoft. Insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) kaynağı olarak erişilebilir
- Application Insights için eski (klasik) platform ve özel ölçümler, Azure portal [Uyarılar (klasik) bölümünde](../../azure-monitor/platform/alerts-classic.overview.md) Şu anda kullanılabilir olarak ve [Microsoft. Insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) kaynağı olarak erişilebilir olarak uyarma
- Daha eski (klasik) hata anomali uyarısı Şu anda Azure portal [Application Insights Içinde akıllı algılama](../../azure-monitor/app/proactive-diagnostics.md) olarak sunulmaktadır; Azure portal [Uyarılar (klasik) bölümünde](../../azure-monitor/platform/alerts-classic.overview.md) gösterilen uyarılarla yapılandırılır

İlgili [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [CLI](../../azure-monitor/platform/alerts-classic-portal.md), [Azure Portal sayfası](../../azure-monitor/platform/alerts-classic-portal.md)ve [kaynak şablonu](../../azure-monitor/platform/alerts-enable-template.md) dahil tüm klasik izleme ve uyarı sistemleri, Ağustos 2019 bitene kadar kullanılabilir olmaya devam edecektir. 

Azure Izleyici 'de Ağustos 2019 ' nin sonunda:

- Klasik izleme ve uyarılar hizmeti kullanımdan kaldırılacak ve yeni uyarı kuralları oluşturmak için artık kullanılamayacak.
- Uyarılar (klasik) ile Ağustos 2019 ' den fazla olmaya devam eden herhangi bir uyarı kuralı, bildirimleri yürütmeye ve yapmaya devam eder, ancak değişiklik için kullanılamaz.
- 2019 Eylül ' den başlayarak, taşınabilecek olan klasik izleme & uyarı kuralları, Microsoft tarafından, birkaç hafta yayılan aşamalarda yeni Azure izleyici platformunda eşdeğerine otomatik olarak taşınır. İşlem herhangi bir kesinti olmadan sorunsuz olacaktır ve müşterilerin izleme kapsamında hiçbir kaybı olmayacaktır.
- Yeni uyarılar platformuna geçirilen uyarı kuralları, izleme kapsamını daha önce olduğu gibi sağlar, ancak yeni yüklerle bildirim tetikleyecektir. Klasik uyarı kuralıyla ilişkili herhangi bir e-posta adresi, Web kancası uç noktası veya mantıksal uygulama bağlantısı, geçirildiğinde ileri taşınır, ancak uyarı yükü yeni platformda farklı olacak şekilde çalışmayabilir.
- [Otomatik olarak geçirilemeyen](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) ve kullanıcılardan el ile eylem gerektiren bazı klasik uyarı kuralları, Haziran 2020 ' e kadar çalışmaya devam edecektir.

> [!IMPORTANT]
> Microsoft Azure Izleyicisi, bazı bilgisayarlarda klasik uyarı kurallarını yeni platforma yakında [geçirmek için aşamalar aracında](alerts-using-migration-tool.md) kullanıma alındı. Ve yine de mevcut olan ve 2019 Eylül 'den başlayarak geçirilebilecek olan tüm klasik uyarı kurallarını zorla aracılığıyla çalıştırın. Müşterilerin, klasik uyarı kuralı yükünün, [Application Insights](#unified-metrics-and-alerts-in-application-insights) veya birleştirilmiş ölçümler ve uyarılardan gelen yeni yükü, [diğer Azure kaynakları için, geçiş sonrası ve uyarılarından](#unified-metrics-and-alerts-for-other-azure-resources)işlemek için uyarlandığı emin olunması gerekir. , klasik uyarı kuralları. Daha fazla bilgi için bkz. [Klasik uyarı kuralı geçişine hazırlanma](alerts-prepare-migration.md)

Bu makale yeni Azure izleme & uyarı işlevselliğiyle ilgili ayrıntıların & ayrıntılarla ve kullanıcılara yeni Azure Izleyici platformunu benimseme konusunda yardımcı olan araçların kullanılabilirliğine yönelik bağlantılarla birlikte sürekli olarak güncelleştirilecektir.

## <a name="pricing-for-migrated-alert-rules"></a>Geçirilen uyarı kuralları fiyatlandırması

Azure Monitor [Klasik uyarılarınızı](../../azure-monitor/platform/alerts-classic.overview.md) yeni uyarılar deneyimine geçirmenize yardımcı olmak için bir geçiş aracı kullanıma sunuyoruz. Geçirilen uyarı kuralları ve bunlara karşılık gelen geçirilen eylem grupları (e-posta, web kancası veya LogicApp) ücretsiz olarak sunulmaya devam eder. Eşiği, toplama türünü ve toplama tanecikliğini düzenleme olanağı da dahil olmak üzere, klasik uyarılarda kullandığınız işlevsellik geçirilen uyarı kuralınızda ücretsiz olarak sunulmaya devam eder. Ancak geçirilen uyarı kuralını, yeni uyarı platformu özelliklerinden birini, bildirimleri veya eylem türlerini kullanması için düzenlerseniz karşılığında bir ücret alınır. Uyarı kuralları ve bildirimlerin fiyatlandırmasıyla ilgili daha fazla bilgi için bkz. [Azure Monitor fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/).

Aşağıdakiler, uyarı kuralınız için ücret uygulanan durumlara örnek gösterilebilir:

- Yeni Azure İzleyici platformunda, ücretsiz birimlerin ötesinde oluşturulan her yeni (geçirilmemiş) uyarı kuralı
- Azure İzleyici’nin kapsadığı ücretsiz birimlerin ötesinde alınan ve tutulan her veri
- Application Insights tarafından gerçekleştirilen her çok testli web testi
- Azure İzleyici’nin kapsadığı ücretsiz birimlerin ötesinde tutulan her özel ölçüm
- Sıklık, birden çok kaynak/boyut, [dinamik eşikler](alerts-dynamic-thresholds.md), kaynak/sinyal değiştirme vb. gibi daha yeni ölçüm uyarısı özelliklerini kullanmak için düzenlenen tüm geçirilmiş uyarı kuralları.
- Daha yeni bildirimleri veya SMS, sesli çağrı ve/veya ıTSM tümleştirmesi gibi eylem türlerini kullanmak için düzenlenen geçirilmiş eylem grupları.

## <a name="next-steps"></a>Sonraki adımlar

* [Yeni Birleşik Azure İzleyicisi](../../azure-monitor/overview.md)hakkında bilgi edinin.
* Yeni [Azure uyarıları](../../azure-monitor/platform/alerts-overview.md)hakkında daha fazla bilgi edinin.

---
title: Akıllı algılama-hata bozuklukları, Application Insights içinde | Microsoft Docs
description: Web uygulamanıza yönelik başarısız isteklerin hızında olağandışı değişiklikler konusunda sizi uyarır ve tanılama analizi sağlar. Yapılandırma gerekmiyor.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.openlocfilehash: f8b8318a16b36593d2fbaf08bcbc19156dc96006
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820577"
---
# <a name="smart-detection---failure-anomalies"></a>Akıllı algılama-hata bozuklukları
[Application Insights](../../azure-monitor/app/app-insights-overview.md) , Web uygulamanız başarısız isteklerin hızında anormal bir artış yaşıyorsa, sizi otomatik olarak neredeyse gerçek zamanlı olarak bilgilendirir. HTTP isteklerinin veya başarısız olarak bildirilen bağımlılık çağrılarının hızında olağan dışı bir artış algılar. İstekler için, başarısız istekler genellikle 400 veya üzeri yanıt kodlarına sahip olanlardır. Sorunu önceliklendirmenize ve tanılamanıza yardımcı olması için, bildirimde hataların ve ilgili telemetrinin özelliklerinin bir analizi verilmiştir. Ayrıca, daha fazla tanılama için Application Insights portalına bağlantılar da vardır. Özelliğin, normal hata oranını tahmin etmek için makine öğrenimi algoritmalarını kullandığından, kurulum veya yapılandırma gerekmez.

Bu özellik, bulutta veya kendi sunucularınızda barındırılan tüm Web uygulamaları için, örneğin, [trackrequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) veya [trackdependency ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)çağrısı yapan bir çalışan rolüne sahipseniz) kullanılır.

[Projeniz için Application Insights](../../azure-monitor/app/app-insights-overview.md)ayarladıktan ve uygulamanız en düşük miktarda telemetri üretdikten sonra, hata Anaklarının akıllı olarak algılanması, uygulamanızın normal davranışını öğrenirken ve gönderebilmesi için 24 saat sürer larınız.

Örnek bir uyarı aşağıda verilmiştir.

![Hata etrafında küme analizini gösteren örnek akıllı algılama uyarısı](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Varsayılan olarak, bu örnekte daha kısa bir biçim postası alırsınız. Ancak, [Bu ayrıntılı biçime geçebilirsiniz](#configure-alerts).
>
>

Size söylediğine dikkat edin:

* Normal uygulama davranışına kıyasla hata oranı.
* Kaç kullanıcının etkilendiğine göre, ne kadar endişelenmeniz gerektiğini biliyoruz.
* Hatalarla ilişkili bir özellik kalıbı. Bu örnekte, belirli bir yanıt kodu, istek adı (işlem) ve uygulama sürümü vardır. Bu, hemen kodunuzda aramaya nereden başlayacağınızı söyler. Diğer olasılıklar belirli bir tarayıcı veya istemci işletim sistemi olabilir.
* Özel durum, günlük izlemeleri ve bağımlılık hatası (veritabanları veya diğer dış bileşenler), ayırt edilen hatalarla ilişkilendirilmiş gibi görünür.
* Application Insights telemetri üzerindeki ilgili aramalara doğrudan bağlantı sağlar.

## <a name="failure-anomalies-v2"></a>Hata Anoıes v2
Hata Anoerleri uyarı kuralının yeni bir sürümü kullanıma sunulmuştur. Bu yeni sürüm yeni Azure uyarı platformunda çalışıyor ve mevcut sürüme yönelik çeşitli geliştirmeler sunuyor.

### <a name="whats-new-in-this-version"></a>Bu sürümdeki yenilikler nelerdir?
- Sorunların daha hızlı algılanması
- Daha zengin bir eylem kümesi-Uyarı kuralı, e-posta ve Web kancası eylemleri içeren "Application Insights akıllı algılama" adlı ilişkili bir [eylem grubuyla](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) oluşturulur ve uyarı tetiklendiğinde ek eylemleri tetiklemek için genişletilebilir.
- Daha fazla odaklanmış bildirim-bu uyarı kuralından gönderilen e-posta bildirimleri artık varsayılan olarak aboneliğin Izleme okuyucu ve katkıda bulunan rollerinin Izlenmesi ile ilişkili kullanıcılara gönderilir. Bu konuda daha fazla bilgiye [buradan](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification)ulaşabilirsiniz.
- ARM şablonları aracılığıyla daha kolay yapılandırma- [buraya](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)örneğe bakın.
- Ortak uyarı şeması desteği-bu uyarı kuralından gönderilen bildirimler [ortak uyarı şemasını](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)izler.
- Birleşik e-posta şablonu-bu uyarı kuralındaki e-posta bildirimlerinin, diğer uyarı türleriyle & tutarlı bir görünüme sahip olması gerekir. Bu değişiklik ile, ayrıntılı tanılama bilgileri ile hata anomali uyarıları edinme seçeneği artık kullanılamaz.

### <a name="how-do-i-get-the-new-version"></a>Yeni sürümü al Nasıl yaparım??
- Yeni oluşturulan Application Insights kaynaklar artık hata anomali uyarı kuralının yeni sürümüyle sağlanıyor.
- Hata anomali uyarı kuralının klasik sürümü olan mevcut Application Insights kaynakları, [Klasik uyarılar kullanımdan kaldırma işleminin](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)bir parçası olarak yeni uyarı platformuna geçirildikten sonra, barındırma aboneliklerinde yeni bir sürüm gönderilir.

> [!NOTE]
> Hata Anoerleri uyarı kuralının yeni sürümü ücretsiz kalır. Ayrıca, ilişkili "Application Insights akıllı algılama" eylem grubu tarafından tetiklenen e-posta ve Web kancası eylemleri de ücretsizdir.
> 
> 

## <a name="benefits-of-smart-detection"></a>Akıllı algılama 'nın avantajları
Sıradan [ölçüm uyarıları](../../azure-monitor/app/alerts.md) bir sorun olabileceğini bildirir. Ancak akıllı algılama, sizin için tanılama işini başlatır, aksi takdirde kendiniz yapmanız gerekir. Düzenli olarak paketlenmiş sonuçları elde edersiniz ve bu da sorunun köküne hızlı bir şekilde ulaşmanıza yardımcı olur.

## <a name="how-it-works"></a>Nasıl çalışır
Akıllı algılama, uygulamanızdan alınan telemetri ve özellikle de hata tarifelerinin izler. Bu kural, `Successful request` özelliğinin yanlış olduğu istek sayısını ve `Successful call` özelliğinin false olduğu bağımlılık çağrılarının sayısını sayar. İstekler için, varsayılan olarak, `Successful request == (resultCode < 400)` (kendi [Trackrequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) aramalarınızı [filtrelemek](../../azure-monitor/app/api-filtering-sampling.md#filtering) veya oluşturmak için özel kod yazmadığınız müddetçe). 

Uygulamanızın performansı tipik bir davranış düzenine sahiptir. Bazı istekler veya bağımlılık çağrıları başkalarından daha fazla hataya neden olur; ve genel hata oranı yük arttıkça devam edebilir. Akıllı algılama, bu anormallikleri bulmak için makine öğrenimini kullanır.

Telemetri Web uygulamanızdan Application Insights geldikçe, akıllı algılama, geçerli davranışı son birkaç gün içinde görülen desenlerle karşılaştırır. Hata oranındaki anormal bir artış önceki performansla karşılaştırılmasıyla gözlemlendiğinde, bir analiz tetiklenir.

Bir analiz tetiklendiğinde, hizmet, hataların nitelediği bir değer modelini belirlemeyi denemek için başarısız istekte bir küme Analizi gerçekleştirir. Yukarıdaki örnekte analiz, çoğu hataların belirli bir sonuç kodu, istek adı, sunucu URL 'SI Konağı ve rol örneği hakkında olduğunu tespit etti. Buna karşılık, analiz, istemci işletim sistemi özelliğinin birden çok değer üzerinde dağıtıldığını tespit etti ve bu nedenle listelenmez.

Hizmetiniz bu telemetri çağrılarında birlikte ayarlandığında, çözümleyici, tanımladığı kümedeki isteklerle ilişkili bir özel durum ve bağımlılık hatası arar ve bu isteklerle ilişkili izleme günlüklerinin bir örneğidir.

Sonuç analizi, bir olarak yapılandırmadığınız sürece size uyarı olarak gönderilir.

[El ile ayarladığınız uyarılar](../../azure-monitor/app/alerts.md)gibi, uyarının durumunu inceleyebilir ve Application Insights kaynağınızın uyarılar dikey penceresinde yapılandırabilirsiniz. Ancak, diğer uyarıların aksine akıllı algılama 'yı ayarlamanıza veya yapılandırmanıza gerek yoktur. İsterseniz, devre dışı bırakabilir veya hedef e-posta adreslerini değiştirebilirsiniz.

### <a name="alert-logic-details"></a>Uyarı mantığı ayrıntıları

Uyarılar, özel makine öğrenimi algoritmız tarafından tetiklenir, bu nedenle tam uygulama ayrıntılarını paylaşamıyoruz. Bu konuda, bazen temeldeki mantığın nasıl çalıştığı hakkında daha fazla bilgi almanız gerektiğini anladık. Bir uyarının tetiklenip tetiklenmeyeceğini belirlemesi için değerlendirilen birincil faktörler şunlardır: 

* 20 dakikalık bir sıralı zaman penceresinde isteklerin/bağımlılıkların hata yüzdesi analizi.
* Son 20 dakikalık hata yüzdesinin son 40 dakikalık ve son yedi günün hızına bir karşılaştırması ve standart sapmanın X-Times ' i aşan önemli sapmaları aramak.
* Uygulamanın istek hacmine/bağımlılıklara göre farklılık gösteren, en düşük hata yüzdesi için uyarlamalı sınır kullanma.

## <a name="configure-alerts"></a>Uyarı yapılandırma
Akıllı algılamayı devre dışı bırakabilir, e-posta alıcılarını değiştirebilir, bir Web kancası oluşturabilir veya daha ayrıntılı uyarı mesajlarını kabul edebilirsiniz.

Uyarılar sayfasını açın. Hata bozuklukları, el ile ayarladığınız uyarılarla birlikte dahil edilmiştir ve şu anda uyarı durumunda olup olmadığını görebilirsiniz.

![Genel Bakış sayfasında uyarılar Kutucuğu ' na tıklayın. Ya da herhangi bir ölçüm sayfasında uyarılar düğmesine tıklayın.](./media/proactive-failure-diagnostics/021.png)

Yapılandırmak için uyarıya tıklayın.

![Yapılandırma](./media/proactive-failure-diagnostics/032.png)

Akıllı algılamayı devre dışı bırakabildiğinize, ancak silemezsiniz (veya başka bir tane oluşturamazsınız).

#### <a name="detailed-alerts"></a>Ayrıntılı uyarılar
"Daha ayrıntılı tanılama al" seçeneğini belirlerseniz, e-postada daha fazla tanılama bilgisi yer alır. Bazen, e-postadaki verilerden yalnızca sorunu tanılayabilirsiniz.

Özel durum ve izleme iletileri içerdiğinden, daha ayrıntılı uyarının hassas bilgiler içermesi gibi küçük bir risk vardır. Ancak, bu yalnızca kodunuz bu iletilere duyarlı bilgilere izin veriyorsa gerçekleşir.

## <a name="triaging-and-diagnosing-an-alert"></a>Uyarı önceliklendirme ve tanılama
Uyarı, başarısız istek oranının anormal bir şekilde algılandığını gösterir. Uygulamanız veya ortamıyla ilgili bir sorun olabilir.

İsteklerin yüzdesinden ve etkilenen kullanıcı sayısına göre, sorunun ne kadar acil olduğuna karar verebilirsiniz. Yukarıdaki örnekte,% 22,5 hata oranı normal bir oran olan %1 oranında karşılaştırıldığı için kötü bir şeyin devam ettiğini gösterir. Öte yandan yalnızca 11 Kullanıcı etkilendi. Uygulamanız olsaydı, ne kadar ciddi olduğunu değerlendirebileceksiniz.

Çoğu durumda, sorunu istek adı, özel durum, bağımlılık hatası ve izleme verilerinden hızlı bir şekilde tanılabileceksiniz.

Bazı ipuçları vardır. Örneğin, bu örnekteki bağımlılık başarısızlık oranı, özel durum oranı (% 89,3) ile aynıdır. Bu, özel durumun doğrudan bağımlılık hatasından ortaya çıkmadığını ve kodunuzda nerede başlayacağımızı açık bir fikir verir.

Daha fazla araştırmak için, her bölümdeki bağlantılar, ilgili istekler, özel durum, bağımlılık veya izlemelerle filtrelenmiş bir [arama sayfasına](../../azure-monitor/app/diagnostic-search.md) doğrudan götürür. Ya da [Azure Portal](https://portal.azure.com)açabilir, uygulamanızın Application Insights kaynağına gidebilir ve sorunlar dikey penceresini açabilirsiniz.

Bu örnekte, ' bağımlılık hatalarının ayrıntılarını görüntüle ' bağlantısına tıkladığınızda Application Insights arama dikey penceresi açılır. Asıl neden örneğine bir örnek olan SQL ifadesini gösterir: null değerler, zorunlu alanlarda sağlanmış ve kaydetme işlemi sırasında doğrulama geçirmedi.

![Tanılama arama](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Son uyarıları gözden geçir

En son uyarıya ulaşmak için **akıllı algılama** ' ye tıklayın:

![Uyarı Özeti](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Fark nedir?..
Hata anomali algılama, Application Insights benzer ancak farklı özellikleri tamamlar.

* [Ölçüm uyarıları](../../azure-monitor/app/alerts.md) sizin tarafınızdan AYARLANıR ve CPU doluluk, istek hızları, sayfa yükleme süreleri vb. gibi çok çeşitli ölçümleri izleyebilirsiniz. Bunları, örneğin daha fazla kaynak eklemeniz gerekiyorsa, sizi uyarmak için kullanabilirsiniz. Bunun aksine, hata anormallerinin akıllı algılanması, Web uygulamanızın başarısız istek hızının Web App 'e kıyasla önemli ölçüde arttığı bir şekilde sizi neredeyse gerçek zamanlı olarak bildirmek üzere tasarlanan, küçük bir kritik ölçüm (Şu anda yalnızca başarısız istek oranı) aralığını ele almaktadır. normal davranış.

    Akıllı algılama, ilgili koşullara yanıt olarak eşiğini otomatik olarak ayarlar.

    Akıllı algılama, tanılama işini sizin için başlatır.
* [Performans anlılıkları akıllı algılamasında](proactive-performance-diagnostics.md) Ayrıca, ölçümlerinizin olağandışı desenlerini bulması için makine zekası kullanılır ve sizin tarafınızdan yapılandırma yapmanız gerekmez. Ancak, hata anormallerinin akıllı algılamalarından farklı olarak, performans anormallerinin akıllı algılamasında kullanılması amaç, kullanım açısından kötü olarak sunulmayan, örneğin belirli bir tarayıcı türü üzerinde belirli sayfalara yönelik olan segmentleri bulmaktan kaynaklanabilir. Analiz her gün gerçekleştirilir ve herhangi bir sonuç bulunursa, bir uyarıdan çok daha az acil olma olasılığı yüksektir. Buna karşılık, hata anormallikleri için analiz, gelen telemetri üzerinde sürekli olarak gerçekleştirilir ve sunucu hata ücretleri beklenenden büyükse dakikalar içinde bilgilendirilirsiniz.

## <a name="if-you-receive-a-smart-detection-alert"></a>Akıllı bir algılama uyarısı alırsanız
*Bu uyarıyı neden aldım?*

* Önceki dönemin normal taban çizgisiyle karşılaştırıldığında başarısız istek hızında anormal bir artış tespit ettik. Hataların ve ilişkili telemetrinin analizinden sonra, Bakılacak bir sorun olduğunu düşündük.

*Bildirimde kesinlikle bir sorun var mı?*

* Uygulama kesintisi veya düşme üzerinde uyarı denemeyi deniyoruz, ancak yalnızca semantiğini ve uygulama ya da kullanıcılar üzerindeki etkisini tam olarak anlayabiliriz.

*Bu nedenle, verilerim göz atacağız mi?*

* Hayır. Hizmet tamamen otomatiktir. Yalnızca bildirimleri alırsınız. Verileriniz [özeldir](../../azure-monitor/app/data-retention-privacy.md).

*Bu uyarıya abone olmam gerekiyor mu?*

* Hayır. İstek Telemetriyi gönderen her uygulamanın akıllı algılama uyarısı kuralı vardır.

*Bunun yerine iş arkadaşlarıma gönderilen bildirimleri iptal edebilir veya alabilir miyim?*

* Evet, uyarı kuralları ' nda, yapılandırmak için akıllı algılama kuralına tıklayın. Uyarıyı devre dışı bırakabilir veya uyarının alıcılarını değiştirebilirsiniz.

*E-postayı kaybettim. Portalda bildirimleri nerede bulabilirim?*

* Etkinlik günlüklerinde. Azure 'da, uygulamanız için Application Insights kaynağını açın ve etkinlik günlükleri ' ni seçin.

*Bazı uyarılar bilinen sorunlarla ilgilidir ve bunları almak istemiyorum.*

* Kapsamımızda uyarı gizliyoruz.

## <a name="next-steps"></a>Sonraki adımlar
Bu tanılama araçları uygulamanızdan Telemetriyi incelemenize yardımcı olur:

* [Ölçüm Gezgini](../../azure-monitor/app/metrics-explorer.md)
* [Arama Gezgini](../../azure-monitor/app/diagnostic-search.md)
* [Analytics-güçlü sorgu dili](../../azure-monitor/log-query/get-started-portal.md)

Akıllı algılamalar tamamen otomatiktir. Ancak daha fazla uyarı kurmak istiyor olabilirsiniz?

* [El ile yapılandırılmış ölçüm uyarıları](../../azure-monitor/app/alerts.md)
* [Kullanılabilirlik Web testleri](../../azure-monitor/app/monitor-web-app-availability.md)

---
title: Akıllı algılama-performans bozuklulıkları | Microsoft Docs
description: Application Insights, uygulama telemetrinizin akıllı analizini yapar ve olası sorunlar hakkında sizi uyarır. Bu özelliğin kurulum yapması gerekmez.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: b9a95bb2ee6ab137e974b46e24738ca5194f3bd2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820574"
---
# <a name="smart-detection---performance-anomalies"></a>Akıllı algılama-performans bozuklukları

[Application Insights](../../azure-monitor/app/app-insights-overview.md) Web uygulamanızın performansını otomatik olarak analiz eder ve olası sorunlar hakkında sizi uyarabilir. Akıllı algılama bildirimlerimizden birini aldığınız için bunu okuyor olabilirsiniz.

Bu özellik, uygulamanızı Application Insights ( [ASP.net](../../azure-monitor/app/asp-net.md), [Java](../../azure-monitor/app/java-get-started.md)veya [Node. js](../../azure-monitor/app/nodejs.md)üzerinde ve [Web sayfası kodunda](../../azure-monitor/app/javascript.md)) yapılandırma dışında özel bir kurulum gerektirmez. Uygulamanız yeterli telemetri ürettirse bu etkin olur.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Ne zaman akıllı bir algılama bildirimi alabilirim?

Application Insights, uygulamanızın performansının şu yollarla düştüğü olduğunu algıladı:

* **Yanıt süresi performansında azalma** -uygulamanız, için kullanılandan daha yavaş isteklere yanıt vermeye başladı. En son dağıtımınızda bir gerileme yapıldığından, değişiklik hızlı bir şekilde yapılmış olabilir. Ya da bir bellek sızıntısı nedeniyle bu dereceli olabilir. 
* **Bağımlılık süresi performansında azalma** -uygulamanız REST API, veritabanı veya başka bir bağımlılığa çağrı yapar. Bağımlılık, için kullanılandan daha yavaş yanıt veriyor.
* **Yavaş performans kalıbı** -uygulamanız yalnızca bazı istekleri etkileyen bir performans sorununa sahip gibi görünüyor. Örneğin, sayfalar bir tür tarayıcıdan diğerlerine göre daha yavaş yükleniyor; ya da istekler belirli bir sunucudan daha yavaş bir şekilde sunulur. Şu anda algoritmalarda sayfa yükleme süreleri, istek yanıt süreleri ve bağımlılık yanıt süreleri göz atalım.  

Akıllı algılama, normal performansın temelini oluşturmak için, uygun bir birimde en az 8 gün telemetri gerektirir. Bu nedenle, uygulamanız bu süre için çalıştıktan sonra, önemli bir sorun bildirime neden olur.


## <a name="does-my-app-definitely-have-a-problem"></a>Uygulamamın kesinlikle bir sorunu var mı?

Hayır, bir bildirim uygulamanızın kesinlikle bir sorun olduğu anlamına gelmez. Yalnızca konuyu daha yakından incelemeniz için bir öneridir.

## <a name="how-do-i-fix-it"></a>Nasıl yaparım? düzeltilsin mi?

Bildirimler tanılama bilgilerini içerir. Bir örneği aşağıda verilmiştir:


![Sunucu yanıt süresi düşme algılamasında bir örnek aşağıda verilmiştir](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Önceliklendirme**. Bildirim, kaç kullanıcının veya kaç işlemin etkilendiğini gösterir. Bu, soruna bir öncelik atamanıza yardımcı olabilir.
2. **Kapsam**. Sorun tüm trafiği mi, yoksa yalnızca bazı sayfaları mı etkiliyor? Belirli tarayıcılarla veya konumlara kısıtlanmış mı? Bu bilgiler bildirimden elde edilebilir.
3. **Tanılayın**. Genellikle, bildirimdeki tanılama bilgileri sorunun doğasını önerir. Örneğin, istek hızı yüksek olduğunda yanıt süresi yavaşlar, bu da sunucunuzu veya bağımlılıklarınızı önerir. 

    Aksi takdirde, Application Insights ' de performans dikey penceresini açın. Burada [Profil Oluşturucu](profiler.md) verileri bulacaksınız. Özel durumlar oluşturulursa, [anlık görüntü hata ayıklayıcıyı](../../azure-monitor/app/snapshot-debugger.md)da deneyebilirsiniz.



## <a name="configure-email-notifications"></a>E-posta bildirimlerini yapılandırma

Akıllı algılama bildirimleri varsayılan olarak etkindir ve [Izleme okuyucusu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) olan ve Application Insights kaynağının bulunduğu aboneliğe [katkıda bulunan erişimi izleyen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) kullanıcılara gönderilir. Bunu değiştirmek için e-posta bildiriminde **Yapılandır** ' a tıklayın veya Application Insights akıllı algılama ayarları ' nı açın. 
  
  ![Akıllı algılama ayarları](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * E-posta bildirimlerinin alınmasını durdurmak için akıllı algılama e-postasında **abonelik kaldırma** bağlantısını kullanabilirsiniz.

Akıllı algılamalar performans bozuklukları hakkındaki e-postalar Application Insights kaynak başına günde bir e-posta ile sınırlandırılmıştır. E-posta yalnızca o gün üzerinde algılanan en az bir yeni sorun varsa gönderilir. Herhangi bir ileti tekrarlanıyor. 

## <a name="faq"></a>SSS

* *Bu nedenle, Microsoft personeli verilerimi baksın mı?*
  * Hayır. Hizmet tamamen otomatiktir. Yalnızca bildirimleri alırsınız. Verileriniz [özeldir](../../azure-monitor/app/data-retention-privacy.md).
* *Application Insights tarafından toplanan tüm verileri analiz edebilir misiniz?*
  * Yok. Şu anda istek yanıt süresini, bağımlılık yanıt süresini ve sayfa yükleme süresini analiz ediyoruz. Ek ölçümlerin çözümlenmesi, ileriye doğru arama kapsamımızda bulunur.

* Bu uygulama türleri ne için çalışır?
  * Bu azaltılmaları, uygun telemetri üreten herhangi bir uygulamada algılanır. Web uygulamanıza Application Insights yüklediyseniz, istekler ve bağımlılıklar otomatik olarak izlenir. Ancak arka uç hizmetlerinde veya diğer uygulamalarda, [Trackrequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) veya [Trackdependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)çağrıları eklediyseniz akıllı algılama aynı şekilde çalışır.

* *Kendi anomali algılama kurallarımı oluşturabilir veya mevcut kuralları özelleştirebilir miyim?*

  * Henüz değil, ancak şunları yapabilirsiniz:
    * Bir metriğin bir eşiği ne zaman kesiştiği hakkında bilgi veren [uyarıları ayarlayın](../../azure-monitor/app/alerts.md) .
    * [Telemetriyi](../../azure-monitor/app/export-telemetry.md) bir [veritabanına](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) veya [Power BI 'a](../../azure-monitor/app/export-power-bi.md )aktarın; burada analiz edebilirsiniz.
* *Analiz ne sıklıkta gerçekleştirildi?*

  * Analizi, önceki günün telemetri gününde (UTC saat diliminde tam gün) çalıştırdık.
* *Bunun için [ölçüm uyarıları](../../azure-monitor/app/alerts.md)değiştirilsin mi?*
  * Hayır.  Olağan dışı olarak düşünebileceğiniz her davranışı saptamak için çalışmayız.


* *Bir bildirime yanıt olarak hiçbir şey yapmadığımda bir anımsatıcı alıyorum?*
  * Hayır, her sorunla ilgili olarak yalnızca bir ileti alırsınız. Sorun devam ederse, akıllı algılama akışı dikey penceresinde güncelleştirilir.
* *E-postayı kaybettim. Portalda bildirimleri nerede bulabilirim?*
  * Uygulamanıza genel bakış Application Insights, **akıllı algılama** kutucuğuna tıklayın. 90 güne kadar olan tüm bildirimleri bulabileceksiniz.

## <a name="how-can-i-improve-performance"></a>Performansı nasıl geliştirebilirim?
Yavaş ve başarısız yanıtlar, kendi deneyiminizden haberdar olduğunuz için Web sitesi kullanıcılarının en büyük zayıflarından biridir. Bu nedenle, sorunları çözmek önemlidir.

### <a name="triage"></a>Değerlendirme
İlk olarak, ne kadar önemlidir? Bir sayfanın yüklenmesi her zaman yavaşsa, ancak sitenize ait kullanıcılarınızın yalnızca %1 ' i göz önünde bulundurmasına rağmen, düşünmek için daha fazla önemli şey vardır. Öte yandan, Kullanıcı yalnızca %1 ' i kullanıyorsa, ancak her seferinde özel durum oluşturur ve bu da araştırma gerektirebilir.

Etki ifadesini (etkilenen kullanıcılar veya trafik) genel bir kılavuz olarak kullanın, ancak tüm hikayenin olmadığını unutmayın. Onaylamak için başka bir kanıt toplayın.

Sorunun parametrelerini göz önünde bulundurun. Coğrafi olarak bağımlıysa, bu bölge dahil olmak üzere [kullanılabilirlik testlerini](../../azure-monitor/app/monitor-web-app-availability.md) ayarlayın: Bu alanda yalnızca ağ sorunları olabilir.

### <a name="diagnose-slow-page-loads"></a>Yavaş sayfa yüklerini Tanıla
Sorun nerede? Sunucu yanıt vermeyi yavaşlatır, sayfa çok uzun mi veya tarayıcının onu görüntülemesi için çok fazla iş yapması gerekiyor mu?

Tarayıcılar ölçüm dikey penceresini açın. Tarayıcı sayfası yükleme süresinin kesimli görünümü saatin nerede gittiğini gösterir. 

* **Gönderme Isteği süresi** yüksekse, sunucu yavaş yanıt verir veya istek çok fazla veri içeren bir gönderiyle yapılır. Yanıt sürelerini araştırmak için [performans ölçümlerine](../../azure-monitor/app/web-monitor-performance.md#metrics) bakın.
* Yavaşlığın dış hizmetler veya veritabanınız olup olmadığını görmek için [bağımlılık izlemeyi](../../azure-monitor/app/asp-net-dependencies.md) ayarlayın.
* **Yanıt alma** özelliği önceden baskın ise sayfanız ve bağımlı parçaları-JAVASCRIPT, CSS, görüntüler vb. (ancak zaman uyumsuz olarak yüklenen veriler) çok uzun olur. Bir [Kullanılabilirlik testi](../../azure-monitor/app/monitor-web-app-availability.md)ayarlayın ve bağımlı bölümleri yükleme seçeneğini ayarladığınızdan emin olun. Bazı sonuçlar aldığınızda, sonucun ayrıntılarını açın ve farklı dosyaların yükleme zamanlarını görmek için genişletin.
* Yüksek **Istemci işlem süresi** , betikleri yavaş çalışıyor olarak önerir. Neden belirgin değilse, bazı zamanlama kodu eklemeyi ve saati trackMetric çağrılarında göndermenizi göz önünde bulundurun.

### <a name="improve-slow-pages"></a>Yavaş sayfaları iyileştirme
Sunucu yanıtlarınızı ve sayfa yükleme sürelerinizi iyileştirmeye yönelik bir Web eksiksiz, bu nedenle hepsini tekrarlamaya çalışmayın. İşte size daha önce bildiğiniz, size düşünmeniz gereken birkaç ipucu:

* Büyük dosyalar nedeniyle yavaş yükleme: betikleri ve diğer parçaları zaman uyumsuz olarak yükleyin. Betik paketlemeyi kullanın. Ana sayfayı, verilerini ayrı olarak yükleyen Pencere öğelerinin içine bölün. Uzun tablolar için düz eski HTML gönderme: verileri JSON veya başka bir sıkıştırma biçiminde istemek için bir betik kullanın, ardından tabloyu yerinde doldurursunuz. Bu konuda yardımcı olacak harika çerçeveler vardır. (Kuşkusuz büyük betikleri de kuyruğa alırlar.)
* Yavaş sunucu bağımlılıkları: bileşenlerinizin coğrafi konumlarını göz önünde bulundurun. Örneğin, Azure kullanıyorsanız, Web sunucusunun ve veritabanının aynı bölgede bulunduğundan emin olun. Sorgular gereksiniminden daha fazla bilgi alır mi? Yardım önbelleğe alınıyor veya toplu işlem yapılsın mı?
* Kapasite sorunları: yanıt sürelerinin sunucu ölçümlerine ve istek sayılarına bakın. Yanıt süreleri, istek sayımlarında tepe noktaları ile orantılı bir şekilde yükseltilip, sunucularınızın uzatılmasından kaynaklanıyor olabilir.


## <a name="server-response-time-degradation"></a>Sunucu yanıt süresi düşme

Yanıt süresi performansında azalma bildirimi size bildirir:

* Bu işlem için normal yanıt süresine kıyasla yanıt süresi.
* Kaç Kullanıcı etkilendi.
* Bu işlem için Ortalama yanıt süresi ve 90. yüzdebirlik yanıt süresi, algılama günü ve 7 gün önce. 
* Algılama günündeki bu işlem isteklerinin ve önceki 7 günün sayısı.
* Bu işlemdeki düşüş ve ilgili bağımlılıklarda azalmaları arasındaki bağıntı. 
* Sorunu tanılamanıza yardımcı olacak bağlantılar.
  * İşlem zamanının nerede harcandığını görüntülemenize yardımcı olacak profil oluşturucu izlemeleri (algılama döneminde bu işlem için profil oluşturucu izleme örnekleri toplanmışsa bağlantı kullanılabilir). 
  * Ölçüm Gezgini 'nde, bu işlem için zaman aralığını/filtrelerini dilimleyerek ve zar duyduğunuz performans raporları.
  * Belirli çağrı özelliklerini görüntülemek için bu çağrıyı arayın.
  * Hata raporları-sayım > 1 Ise, bu işlemde performans düşüşüne katkıda bulunan hatalar olduğunu ifade ediyor.

## <a name="dependency-duration-degradation"></a>Bağımlılık süresi düşme

Birçok durumda, dış hizmetlerde ağır güvenilirlik sunan modern uygulamalar daha fazla ve daha fazla açık mikro hizmet tasarımı yaklaşımı. Örneğin, uygulamanız bazı veri platformlarını kullanıyorsa veya kendi bot hizmetinizi derseniz bile, botlarınızın daha fazla insan yoluyla etkileşim kurmasını sağlamak için bazı bilişsel hizmetler sağlayıcısına geçiş yapabilirsiniz m.  

Örnek bağımlılık performansında azalma bildirimi:

![Bağımlılık süresi düşme algılamasında bir örnek aşağıda verilmiştir](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Size söylediğine dikkat edin:

* Bu işlem için normal yanıt süresine kıyasla geçen süre
* Kaç Kullanıcı etkilendi
* Bu bağımlılık için Ortalama süre ve 90. yüzdebirlik süresi, algılama günü ve 7 gün önce
* Algılama günü ve 7 gün öncesine ait bağımlılık çağrısı sayısı
* Sorunu tanılamanıza yardımcı olacak bağlantılar
  * Bu bağımlılık için ölçüm Gezgininde performans raporları
  * Çağrıların özelliklerini görüntülemek için bu bağımlılık çağrılarını arayın
  * Hata raporları-sayım > 1 Ise, süre azalmasına katkıda bulunan algılama döneminde başarısız bağımlılık çağrılarının olduğu anlamına gelir. 
  * Bu bağımlılık süresini ve sayısını hesaplayan sorgularla açık çözümlemeler  

## <a name="smart-detection-of-slow-performing-patterns"></a>Yavaş performanslı desenlerin akıllı algılanması 

Application Insights kullanıcılarınızın yalnızca bazı parçalarını etkileyebilecek veya bazı durumlarda yalnızca kullanıcıları etkileyebilecek performans sorunları buluyor. Örneğin, sayfa yükleme hakkında bildirim, diğer tarayıcı türlerinden farklı bir tür tarayıcıdan daha yavaştır veya istekler belirli bir sunucudan daha yavaş sunulduysa. Ayrıca, belirli işletim sistemi kullanan istemciler için bir coğrafi alanda yavaş sayfa yükleri gibi özellikler birleşimleriyle ilişkili sorunları da bulabilir.  

Bunlar gibi bozukluklar yalnızca verileri inceleyerek göz önünde bulundurmanın çok zor olduğu, ancak düşündüğünüzden daha yaygın bir işlem. Genellikle, müşterileriniz şikayet edildiğinde yalnızca yüzey olur. Bu süre içinde çok geç: etkilenen kullanıcılar zaten rakiplerinize geçiş yapıyor!

Şu anda algoritmalarımız sayfa yükleme süreleriyle, sunucuda yanıt süreleri isteğiyle ve bağımlılık yanıt süreleriyle aynı şekilde görünür.  

Herhangi bir eşik ayarlamanıza veya kural yapılandırmanıza gerek yoktur. Makine öğrenimi ve veri araştırma algoritmaları anormal desenleri algılamak için kullanılır.

![E-posta uyarısında, Azure 'da Tanılama raporunu açmak için bağlantıya tıklayın](./media/proactive-performance-diagnostics/03.png)

* Sorunun algılandığı **zamanı gösterir.**
* Şunları **açıklar:**

  * Algılanan sorun;
  * Bulduğumuz olay kümesinin Özellikler sorun davranışını görüntülendi.
* Tablo kötü performanslı kümeyi diğer tüm olayların ortalama davranışına göre karşılaştırır.

Ölçüm Gezgini 'ni açmak için bağlantılara tıklayın ve yavaş çalışan kümesinin saat ve özelliklerinde filtrelenmiş ilgili raporlarda arama yapın.

Telemetriyi araştırmak için zaman aralığını ve filtreleri değiştirin.

## <a name="next-steps"></a>Sonraki adımlar
Bu tanılama araçları uygulamanızdan Telemetriyi incelemenize yardımcı olur:

* [Profil Oluşturucu](profiler.md) 
* [Anlık görüntü hata ayıklayıcısı](../../azure-monitor/app/snapshot-debugger.md)
* [Analizler](../../azure-monitor/log-query/get-started-portal.md)
* [Analytics akıllı tanılama](../../azure-monitor/app/analytics.md)

Akıllı algılamalar tamamen otomatiktir. Ancak daha fazla uyarı kurmak istiyor olabilirsiniz?

* [El ile yapılandırılmış ölçüm uyarıları](../../azure-monitor/app/alerts.md)
* [Kullanılabilirlik Web testleri](../../azure-monitor/app/monitor-web-app-availability.md)

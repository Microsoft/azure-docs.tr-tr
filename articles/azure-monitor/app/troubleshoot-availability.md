---
title: Azure Application Insights kullanılabilirlik testlerinizde sorun giderin | Microsoft Docs
description: Azure Application Insights 'de Web testlerinde sorun giderme. Web sitesi kullanılamaz duruma gelirse veya yavaş yanıt verirse uyarı alın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 71c16fa005710bb5816ec69716573b79fcae620a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899536"
---
# <a name="troubleshooting"></a>Sorun giderme

Bu makale, kullanılabilirlik izlemeyi kullanırken oluşabilecek yaygın sorunları gidermenize yardımcı olur.

## <a name="ssltls-errors"></a>SSL/TLS hataları

|Belirti/hata iletisi| Olası nedenler|
|--------|------|
|SSL/TLS güvenli kanalı oluşturulamadı  | SSL sürümü. Yalnızca TLS 1,0, 1,1 ve 1,2 desteklenir. **SSLv3 desteklenmiyor.**
|TLSv 1.2 kayıt katmanı: uyarı (düzey: önemli, Açıklama: Hatalı kayıt MAC)| [Daha fazla bilgi](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)için bkz. StackExchange iş parçacığı.
|Başarısız olan URL bir CDN 'ye (Content Delivery Network) | Bunun nedeni CDN 'inizdeki bir yanlış yapılandırma olabilir |  

### <a name="possible-workaround"></a>Olası geçici çözüm

* Sorunu yaşayan URL 'Ler her zaman bağımlı kaynaklara bağımlıysa, Web testi için **bağımlı istekleri ayrıştırmayı** devre dışı bırakmanız önerilir.

## <a name="test-fails-only-from-certain-locations"></a>Test yalnızca belirli konumlardan başarısız oluyor

|Belirti/hata iletisi| Olası nedenler|
|----|---------|
|Bağlı olan taraf bir süre sonra düzgün bir şekilde yanıt vermediği için bağlantı girişimi başarısız oldu  | Belirli konumlardaki test aracıları bir güvenlik duvarı tarafından engelleniyor.|
|    |Belirli IP adreslerini yeniden yönlendirme (yük dengeleyiciler, coğrafi trafik yöneticileri, Azure Express Route) aracılığıyla yapılır. 
|    |Azure ExpressRoute kullanıyorsanız, paketlerin [asimetrik yönlendirmenin gerçekleştiği](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)durumlarda nerelerde bırakılbileceği senaryolar vardır.|

## <a name="test-failure-with-a-protocol-violation-error"></a>Protokol ihlali hatası ile test hatası

|Belirti/hata iletisi| Olası nedenler| Olası çözümler |
|----|---------|-----|
|Sunucu bir protokol ihlali taahhütlü. Section = ResponseHeader ayrıntısı = CR 'nin arkasından LF gelmelidir | Bu durum, hatalı biçimlendirilmiş üstbilgiler algılandığında oluşur. Özellikle, bazı üst bilgiler satır sonunu belirtmek için CRLF kullanmıyor olabilir, bu da HTTP belirtimini ihlal ediyor. Application Insights, bu HTTP belirtimini zorluyor ve hatalı biçimlendirilmiş üst bilgilerle yanıt verir.| a. Hatalı sunucuları onarmak için Web sitesi konak sağlayıcısı/CDN sağlayıcısına başvurun. <br> b. Başarısız isteklerin kaynak olması durumunda (örn. stil dosyaları, görüntüler, betikler), bağımlı isteklerin ayrıştırılmasını devre dışı bırakmayı düşünebilirsiniz. Bunu yaparsanız, bu dosyaların kullanılabilirliğini izleme özelliğini kaybedeceğinizi aklınızda bulundurun).

> [!NOTE]
> URL, HTTP üstbilgileri gevşek olarak doğrulanmış tarayıcılarda başarısız olmayabilir. Bu sorunun ayrıntılı bir açıklaması için bu blog gönderisine bakın: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Genel sorun giderme soruları

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Site sorunsuz görünüyor ancak sınama arızalarını görüyorum mi? Neden Application Insights uyarı veriyor?

   * Testiniz **bağımlı Istekleri Ayrıştır** mi? Bu, komut dosyaları, görüntüler vb. kaynaklar üzerinde katı bir denetim elde ediyor. Bu tür hataların bir tarayıcıda dikkat çekici olmaması olabilir. Tüm görüntüleri, betikleri, stil sayfalarını ve sayfa tarafından yüklenen diğer dosyaları denetleyin. Bunlardan herhangi biri başarısız olursa, ana HTML sayfası sorun olmadan yüklense bile test başarısız olarak bildirilir. Testi bu tür kaynak hatalarıyla desensitize için, test yapılandırmasından gelen bağımlı Istekleri Ayrışmanızın işaretini kaldırmanız yeterlidir.

   * Geçici ağ hüklarından gürültü düzeyini azaltmak için, test arızaları yapılandırması için yeniden denemeleri etkinleştirin ' in işaretli olduğundan emin olun. Ayrıca, daha fazla konumdan test edebilir ve uyarı kuralı eşiğini, hataya özel sorunları engellemek için uygun şekilde yönetebilirsiniz.

   * Hatanın neden bildirildiğimiz ayrıntılarını görmek için kullanılabilirlik deneyiminde kırmızı noktalardan herhangi birine veya arama Gezgini 'ndeki herhangi bir kullanılabilirlik hatasına tıklayın. Test sonucu, ilişkili sunucu tarafı telemetri (etkinse) ile birlikte testin neden başarısız olduğunu anlamanıza yardımcı olmalıdır. Geçici sorunların yaygın nedenleri ağ veya bağlantı sorunlarıdır.

   * Test zaman aşımına uğrar mı? 2 dakika sonra testleri iptal ediyoruz. Ping veya çok adımlı testiniz 2 dakikadan uzun sürerse, bunu hata olarak raporlarız. Testi daha kısa süreler içinde tamamlayabileceğiniz birden çok tabloya bölmek göz önünde bulundurun.

   * Tüm konumlar hata verdi mi, yoksa yalnızca bir kısmı mı? Yalnızca bazı sorunlar bildirilmezse, bu durum ağ/CDN sorunlarından kaynaklanabilir. Daha sonra, kırmızı noktalara tıklanması, konumun neden hatalara neden olduğunu anlamanıza yardımcı olmalıdır.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Uyarı tetiklendiğinde veya her ikisini de bir e-posta almadı mi?

E-postanızı doğrudan listelendiğini doğrulamak için klasik uyarılar yapılandırmasını denetleyin veya açık olduğunuz bir dağıtım listesi bildirimleri alacak şekilde yapılandırılır. Bu durumda, dağıtım listesi yapılandırmasını denetleyerek dış e-posta alabileceğinden emin olun. Ayrıca, e-posta yöneticinizin bu soruna neden olabilecek bir ilke yapılandırılıp yapılandırılmadığını denetleyin.

### <a name="i-did-not-receive-the-webhook-notification"></a>Web kancası bildirimi aldım mı?

Web kancası bildirimini alan uygulamanın kullanılabilir olduğundan ve Web kancası isteklerini başarıyla işlediğinden emin olun. Daha fazla [bilgi için bkz](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) .

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Protokol ihlali hatası ile aralıklı test hatası mı?

Hata ("protokol ihlali.CR’den sonra LF gelmelidir") sunucu (veya bağımlılıklar) ile ilgili bir sorun olduğunu gösterir. Bu durum, yanıtta hatalı biçimlendirilmiş üst bilgiler ayarlandığında meydana gelir. Yük dengeleyiciler veya CDN'lerden kaynaklanabilir. Özellikle, bazı üst bilgiler satır sonunu belirtmek için CRLF kullanmıyor olabilir, bu da HTTP belirtimini ihlal eder ve bu nedenle .NET WebRequest düzeyinde doğrulama başarısız olur. İhlal halinde olabilecek nokta üst bilgilerine olan yanıtı inceleyin.

> [!NOTE]
> URL, HTTP üstbilgileri gevşek olarak doğrulanmış tarayıcılarda başarısız olmayabilir. Bu sorunun ayrıntılı bir açıklaması için bu blog gönderisine bakın: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Test başarısızlıklarını tanılamak için ilgili sunucu tarafı telemetri görmüyorum? *

Sunucu tarafı uygulamanız için Application Insights ayarlanmışsa, bunun nedeni [örnekleme](../../azure-monitor/app/sampling.md) işleminin devam ediyor olması olabilir. Farklı bir kullanılabilirlik sonucu seçin.

### <a name="can-i-call-code-from-my-web-test"></a>Web testimden kod çağırabilir miyim?

Hayır. Test adımları .webtest dosyasında olmalıdır. Bu nedenle, başka web testlerini çağıramaz, döngüleri kullanamazsınız. Ancak yararlı bulabileceğiniz bir dizi eklenti vardır.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>"Web testleri" ve "kullanılabilirlik testleri" arasında bir fark var mı?

Bu iki terim birbirlerinin yerine kullanılabilir. Kullanılabilirlik testleri, çok adımlı web testlerine ek olarak tek URL ping testlerini de içeren daha genel bir terimdir.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Kullanılabilirlik testlerini, güvenlik duvarının arkasında çalışan kendi dahili sunucumuzda kullanmayı tercih ediyorum.

   İki olası çözümü vardır:

   * Güvenlik duvarınızı, [Web testi aracılarımızın IP adreslerinden](../../azure-monitor/app/ip-addresses.md) gelen isteklere izin verecek şekilde yapılandırın.
   * İç sunucunuzu düzenli olarak test etmek için kendi kodunuzu yazın. Kodu, güvenlik duvarınızın arkasındaki bir test sunucusunda arka plan işlemi olarak çalıştırın. Test işleminiz, temel SDK paketindeki [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API’sini kullanarak sonuçları Application Insights’a gönderebilir. Bunun için test sunucunuzun Application Insights alım uç noktası ile giden bağlantısının olması gerekir, ancak bu, gelen isteklere izin vermeye göre çok daha küçük bir güvenlik riski oluşturur. Sonuçlar kullanılabilirlik Web testleri dikey penceresinde görünür ancak deneyim Portal aracılığıyla oluşturulan testler için kullanılabilir olan yeniliklerden biraz basitleşmiş olur. Özel kullanılabilirlik testleri analiz, arama ve ölçümler ' de kullanılabilirlik sonuçları olarak da görünür.

### <a name="uploading-a-multi-step-web-test-fails"></a>Çok adımlı web testi yüklenemiyor

Bunun gerçekleşebileceği bazı nedenler:
   * 300 K boyut sınırı vardır.
   * Döngüler desteklenmez.
   * Başka web testlerine başvurular desteklenmez.
   * Veri kaynakları desteklenmez.

### <a name="my-multi-step-test-doesnt-complete"></a>Çok adımlı testim tamamlanmadı

Test başına 100 istek sınırı var. Ayrıca, iki dakikadan uzun süre çalışırsa test durdurulur.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>İstemci sertifikasıyla testi nasıl çalıştırırım?

Bu şu anda desteklenmiyor.

## <a name="who-receives-the-classic-alert-notifications"></a>(Klasik) uyarı bildirimlerini kim alıyor?

Bu bölüm yalnızca, klasik uyarılar için geçerlidir ve yalnızca istediğiniz alıcıların bildirimleri almasını sağlamak için uyarı bildirimlerinizi iyileştirmenize yardımcı olur. [Klasik uyarılar](../platform/alerts-classic.overview.md)arasındaki fark hakkında daha fazla bilgi edinmek ve yeni uyarılar deneyimi hakkında daha fazla bilgi edinmek için [uyarılara genel bakış makalesine](../platform/alerts-overview.md)bakın. Yeni uyarılar deneyiminde uyarı bildirimini denetlemek için [eylem gruplarını](../platform/action-groups.md)kullanın.

* Klasik uyarı bildirimleri için belirli alıcıların kullanılmasını öneririz.

* X-Y konumlarından oluşan hatalarda oluşan uyarılarda, **toplu/grup** onay kutusu seçeneği etkinleştirildiyse, yönetici/ortak yönetici rollerine sahip kullanıcılara gönderir.  Esas olarak, _aboneliğin_ _Tüm_ yöneticileri bildirim alır.

* Kullanılabilirlik ölçümlerinde uyarılar için **toplu/grup** onay kutusu seçeneği etkinleştirilirse, abonelikte sahip, katkıda bulunan veya okuyucu rollerine sahip kullanıcılara gönderir. Aslında, aboneliğe erişimi olan _Tüm_ kullanıcılar Application Insights kaynak kapsamdadır ve bildirimler alacaktır. 

> [!NOTE]
> Şu anda **toplu/grup** onay kutusu seçeneğini kullanırsanız ve devre dışı bıraktığınızda, değişikliği döndüremezsiniz.

Kullanıcılara rollerine göre bildirimde bulunan yeni uyarı deneyimini veya neredeyse gerçek zamanlı uyarıları kullanın. [Eylem gruplarıyla](../platform/action-groups.md), katkıda bulunan/sahip/okuyucu rollerinin herhangi birine sahip kullanıcılar için e-posta bildirimleri yapılandırabilirsiniz (tek bir seçenek olarak birlikte birleştirilemez).

## <a name="next-steps"></a>Sonraki adımlar

* [Çok adımlı Web testi](availability-multistep.md)
* [URL ping testleri](monitor-web-app-availability.md)

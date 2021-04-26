---
title: JavaScript Web uygulamaları için SDK yükleme hatası giderme-Azure Application Insights
description: JavaScript Web uygulamaları için SDK yükleme başarısızlığı sorunlarını giderme
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6295a56abbf3466c68b968c935936dbc10e22fb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711425"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>JavaScript Web uygulamaları için SDK yükleme hatası giderme

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

SDK yükleme hatası özel durumu, SDK betiğinin indirileceği veya başlatılamadığından JavaScript kod parçacığı (v3 veya üzeri) tarafından oluşturulur ve raporlanır. Simplelistik, son kullanıcılarınızın istemcisi (tarayıcısı) Application Insights SDK 'yı indiremedi veya tanımlı barındırma sayfasından başlatamıyor, bu nedenle hiçbir telemetri veya olay raporlanmadı.

![Azure portal tarayıcısı hatasına genel bakış](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Bu özel durum, Fetch () API veya XMLHttpRequest ' i destekleyen tüm büyük tarayıcılarda desteklenir. Bu, IE 8 ve daha fazlasını dışladığı için bu tarayıcılardan bildirilen özel durum türünü (ortamınız bir Fetch polyfill içermiyorsa) almaz.

![tarayıcı özel durum ayrıntısı](./media/javascript-sdk-load-failure/exception.png)

Yığın ayrıntıları, son kullanıcı tarafından kullanılan URL 'lerle ilgili temel bilgileri içerir.

| Ad                      | Açıklama                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN &nbsp; uç noktası&gt; | SDK 'Yı indirmek için kullanılan URL (ve başarısız).                                                      |
| &lt;Yardım &nbsp; bağlantısı&gt;    | Sorun giderme belgelerine (Bu sayfa) bağlanan bir URL.                                              |
| &lt;Ana bilgisayar &nbsp; URL 'si&gt;     | Son kullanıcının kullandığı sayfanın URL 'SI.                                                    |
| &lt;Uç nokta &nbsp; URL 'si&gt; | Özel durumu raporlamak için kullanılan URL, bu değer barındırma sayfasına genel İnternet 'ten mi yoksa özel bir buluttan mi erişildiğine ilişkin yararlı olabilir.

Bu özel durumun gerçekleşmesi için en yaygın nedenler şunlardır:

- Aralıklı ağ bağlantısı hatası.
- CDN kesintisi Application Insights.
- Betiği yükledikten sonra SDK başlatılamadı.
- JavaScript CDN Application Insights engellenmiş.

[Aralıklı ağ bağlantısı hatası](#intermittent-network-connectivity-failure) , özellikle kullanıcıların ağ bağlantısını zaman zaman kaybetmediği Mobil dolaşım senaryolarında bu özel durumun en yaygın nedenidir.

Aşağıdaki bölümlerde, bu hatanın olası her bir nedeni nasıl giderebileceğiniz açıklanır.

> [!NOTE]
> Sorun giderme adımlarının birkaçı, uygulamanızın kod parçacığı &lt; betiği/etiketi üzerinde doğrudan denetime sahip olduğunu &gt; ve barındırma HTML sayfasının bir parçası olarak döndürülen yapılandırma olduğunu varsayar. Bu durumda, söz konusu tanımlı adımlar senaryonuz için uygulanmaz.

## <a name="intermittent-network-connectivity-failure"></a>Aralıklı ağ bağlantısı hatası

**Kullanıcı aralıklı ağ bağlantısı hatalarıyla karşılaşıyorsa, daha az olası çözüm vardır ve bunlar genellikle kısa bir süre boyunca kendilerini çözirler.** Örneğin, kullanıcı sitenizi yeniden yüklediğinde (sayfayı yenilediğinde), güncelleştirilmiş bir sürüm yayınlanana kadar dosyalar (sonunda) indirilir ve yerel olarak önbelleğe alınır.

> [!NOTE]
> Bu özel durum kalıcıdır ve bazı kullanıcılarınız genelinde (Bu özel durumun raporlanan hızlı ve sürekli bir düzeyi ile tanılandı), normal istemci telemetrisinde bir azalmayla birlikte, aralıklı ağ bağlantısı sorunları sorunun gerçek nedeni _değildir_ ve diğer bilinen sorunları tanılamaya devam etmeniz gerekir.

Bu durumla birlikte kendi CDN 'niz üzerinde SDK 'Yı barındırmak, kendi CDN 'niz aynı sorundan etkilenmediği için bu özel durumun tekrarlamalarını sağlamak veya azaltmak düşüktür.

Aynı zamanda, NPM paketleri çözümü aracılığıyla SDK kullanılırken de geçerlidir. Bununla birlikte, bu durum oluştuğunda son kullanıcılar perspektifinden, tüm uygulamanız yükleme/başlatma işlemi başarısız olur ( _yalnızca_ görsel olarak görmedikleri TELEMETRI SDK 'sı yerine), bu nedenle tamamen yüklenene kadar sitenizi yenileyecektir.

Ayrıca, Application Insights SDK 'Yı eklemek için [NPM paketlerini](#use-npm-packages-to-embed-the-application-insight-sdk) kullanmayı deneyebilirsiniz.

Aralıklı ağ bağlantısı başarısızlığını en aza indirmek için, tüm CDN dosyalarında Cache-Control üst bilgileri uyguladık. böylece, son kullanıcının tarayıcısı SDK 'nın geçerli sürümünü indirdikten sonra indirmesi gerekmez ve tarayıcı daha önce elde edilen kopyayı yeniden kullanacaktır (bkz. [önbelleğe alma nasıl çalışır](../../cdn/cdn-how-caching-works.md)). Önbelleğe alma denetimi başarısız olursa veya yeni bir sürüm varsa, son kullanıcının tarayıcısının güncelleştirilmiş sürümü indirmesi gerekir. Bu nedenle, denetim hatası senaryosunda _"gürültü"_ için bir arka plan düzeyi veya yeni bir yayın gerçekleştiğinde ve genel kullanıma SUNULDUĞUNDA (CDN 'e dağıtılır) geçici bir ani bir işlem görebilirsiniz.
 
## <a name="application-insights-cdn-outage"></a>CDN kesintisi Application Insights

CDN uç noktasına doğrudan tarayıcıdan (örneğin, https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js https://js.monitor.azure.com/scripts/b/ai.2.min.js) son kullanıcılarınızın büyük bir bir konumdan) (örneğin, kuruluşunuzun bu etki alanını engellemediğini varsayarak) erişimi deneyerek BIR Application Insights CDN kesintisi olup olmadığını doğrulayabilirsiniz.

Bir kesinti olduğunu onaylamak için [Yeni bir destek bileti oluşturabilir](https://azure.microsoft.com/support/create-ticket/) veya SDK 'yı indirmek için kullanılan URL 'yi değiştirmeyi deneyebilirsiniz.

### <a name="change-the-cdn-endpoint"></a>CDN uç noktasını değiştirme
  
Kod parçacığı ve yapılandırması uygulamanız tarafından oluşturulan her sayfanın parçası olarak döndürüldüğünden, kod parçacığı `src` YAPıLANDıRMASıNı SDK için farklı BIR URL kullanacak şekilde değiştirebilirsiniz. Bu yaklaşımı kullanarak, yeni URL 'nin engellenmediği için CDN engellenmiş sorunu atlayabilirsiniz.

Geçerli Application Insights JavaScript SDK CDN uç noktaları
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/`Uç noktası, herhangi bir yapılandırmayı değiştirmenize gerek olmadan yaklaşık 5 dakika IÇINDE CDN sağlayıcıları arasında geçiş yapmamızı sağlayan bir diğer addır. Bu, bir CDN sağlayıcısı bölgesel veya küresel sorunları herkesin ayarlarını ayarlamasına gerek duymadan daha hızlı çözmemize olanak tanımak için.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>Betiği yükledikten sonra SDK başlatılamadı

SDK başlatılamadı, &lt; betik/ &gt; CDN 'den başarıyla indirildi ancak başlatma sırasında başarısız oluyor. Bunun nedeni eksik veya geçersiz bağımlılıklardan ya da bazı JavaScript özel durumları olabilir.

Denetlenecek ilk şey, SDK 'nın başarıyla indirilip indirilmediğini, betik indirilmediğini, bu senaryonun SDK yükleme hatası özel durumunun nedeni __değildir__ .

Hızlı denetim: geliştirici araçları 'nı (F12) destekleyen bir tarayıcı kullanarak, ```src``` kod parçacığı yapılandırmasında tanımlanan betiğin 200 (başarılı) veya 304 (değiştirilmedi) yanıt kodu ile indirildiğini Ağ sekmesinde doğrulayın. Ağ trafiğini gözden geçirmek için Fiddler gibi bir araç da kullanabilirsiniz.

Aşağıdaki bölümlerde farklı raporlama seçenekleri bulunur. Bu, bir destek bileti oluşturmayı veya GitHub 'da bir sorun oluşturmanızı öneririz.

 Temel raporlama kuralları:

- Sorun yalnızca az sayıda kullanıcıyı ve belirli bir tarayıcı sürümü veya alt kümesini etkileirse (bildirilen özel durum hakkındaki ayrıntıları kontrol edin), büyük olasılıkla bir son kullanıcı veya yalnızca ortam sorunudur ve bu da uygulamanızın ek uygulamalar sağlaması için gerekli olacaktır `polyfill` . Bu dosyalar için [GitHub 'da bir sorun](https://github.com/Microsoft/ApplicationInsights-JS/issues)yapın.
- Bu sorun, tüm uygulamanızı etkiliyorsa ve tüm kullanıcılarınız etkileniyorsa, büyük olasılıkla bir yayın ile ilgili sorun oluşur ve bu nedenle [Yeni bir destek bileti oluşturmanız](https://azure.microsoft.com/support/create-ticket/)gerekir.

### <a name="javascript-exceptions"></a>JavaScript özel durumları

İlk olarak, geliştirici araçlarını destekleyen bir tarayıcı kullanarak (F12) JavaScript özel durumlarını kontrol etmenizi sağlar.

SDK komut dosyasında (örneğin ai.2.min.js) bildirilen özel durumlar varsa, bu, SDK 'ya geçirilen yapılandırmanın beklenmeyen veya eksik gerekli yapılandırmayı içerdiğini ya da CDN 'ye hatalı bir yayın dağıtıldığını gösteriyor olabilir.

Hatalı yapılandırmayı denetlemek için, kod parçacığına geçirilen yapılandırmayı değiştirin (henüz yoksa) ve yalnızca izleme anahtarınızı bir dize değeri olarak içerecek şekilde değiştirin.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> ınstrumentationkey: "INSTRUMENTATION_KEY"<br />
> }});<br />

Bu en az yapılandırmayı kullanırken, yine de SDK komut dosyasında bir JavaScript özel durumu görüyorsanız, yeni bir dağıtım [bileti oluşturun](https://azure.microsoft.com/support/create-ticket/) çünkü bunun nedeni, yeni dağıtılan bir sürümle ilgili bir sorun olabilir.

Özel durum kaybolursa, sorun büyük olasılıkla tür uyuşmazlığı veya beklenmeyen bir değer olabilir. Özel durum tekrar gerçekleşene kadar yapılandırma seçeneklerinizi bir kez daha geri eklemeye başlayın ve test edin. Sonra soruna neden olan öğenin belgelerini denetleyin. Belgeler net değilse veya yardıma ihtiyacınız varsa [GitHub 'da bir sorun oluştu](https://github.com/Microsoft/ApplicationInsights-JS/issues).

Yapılandırmanız daha önce dağıtılırsa, ancak bu özel durumu raporlamaya başladıysa, yeni dağıtılan bir sürümle ilgili bir sorun olabilir, yalnızca küçük bir kullanıcı/tarayıcı kümesini etkileyip etkilemediğini ve [GitHub üzerinde bir sorun](https://github.com/Microsoft/ApplicationInsights-JS/issues) mı, yoksa  [Yeni bir destek bileti mi oluşturmadığını](https://azure.microsoft.com/support/create-ticket/)kontrol edin.

### <a name="enable-console-debugging"></a>Konsol hata ayıklamayı etkinleştir

Oluşturulan bir özel durum olmadığı varsayılarak, bir sonraki adımda ayarı yapılandırmaya ekleyerek konsol hata ayıklamanın etkinleştirilmesi `loggingLevelConsole` , bu, tüm başlatma hatalarını ve uyarılarını tarayıcılar konsoluna gönderir (normalde geliştirici araçları (F12) aracılığıyla kullanılabilir). Bildirilen tüm hatalar kendi kendine açıklayıcı olmalıdır ve [GitHub 'da sorun dosyaya](https://github.com/Microsoft/ApplicationInsights-JS/issues)daha fazla yardıma ihtiyacınız varsa.

> cfg: {<br />
> ınstrumentationkey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Başlatma sırasında SDK, bilinen ana bağımlılıklar için bazı temel denetimler gerçekleştirir. Bunlar geçerli çalışma zamanı tarafından sağlanmadıysa, hata iletisini konsola uyarı iletileri olarak bildirir, ancak yalnızca, `loggingLevelConsole` sıfırdan büyükse olur.

Hala başlatılamıyorsa, ```enableDebug``` yapılandırma ayarını etkinleştirmeyi deneyin. Bu, tüm iç hataların özel durum olarak oluşturulmasına neden olur (Bu, telemetrinin kaybolmasına neden olur). Bu yalnızca bir geliştirici olduğundan, bazı iç denetimlerin parçası olarak oluşan özel durumlarla birlikte çok gürültülü bir şekilde yararlanacaktır. bu nedenle, hangi sorunun SDK 'nın başarısız olmasına neden olduğunu anlamak için her bir özel durumu gözden geçirmeniz gerekir. Betiğin küçültülmüş olmayan sürümünü kullanın (". js" öğesinin altındaki uzantıyı not edin ve ".min.js" değil), aksi takdirde özel durumlar okunamaz olur.

> [!WARNING]
> Bu yalnızca geliştirici ayarıdır ve Telemetriyi yitideceğinizi tam bir üretim ortamında hıç etkinleştirilmemelidir.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> ınstrumentationkey: "INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

Bu, yine de öngörü sağlamıyorsa, Ayrıntılar ve varsa örnek bir siteyle [GitHub üzerinde bir sorun](https://github.com/Microsoft/ApplicationInsights-JS/issues) yapmanız gerekir. Sorunu belirlemenize yardımcı olmak için tarayıcı sürümünü, işletim sistemini ve JS Framework 'ün ayrıntılarını ekleyin.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>JavaScript CDN Application Insights engellendi

Application Insights bir JavaScript SDK CDN uç noktası raporlanmışsa ve/veya güvenli değil olarak tanımlandığında, engellenmekte olan CDN mümkündür. Bu gerçekleştiğinde, uç noktanın genel olarak engellenmesine neden olur ve bu listelerin müşterileri tüm erişimleri engellemeye başlar.

Sorunu çözmek için, CDN uç noktasının sahibinin, uç noktayı güvenli olmayan olarak işaretli blok listeleme varlığıyla çalışması gerekir, böylece ilgili listeden kaldırılabilirler.

CDN uç noktasının güvenli değil olarak belirlenmediğini denetleyin.
- [Google saydam raporu](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

Uygulamanın, güvenlik duvarının veya ortamın Bu listelerin yerel kopyalarını güncelleştirdiğine bağlı olarak, son kullanıcılar veya kurumsal BT departmanlarının bir güncelleştirmeyi zorlamak veya sorunu çözmesi için açıkça izin vermesi gerekir.

CDN uç noktası unsafe olarak tanımlanmışsa, sorunun mümkün olan en kısa sürede çözümlendiğinden emin olmak için [bir destek bileti oluşturun](https://azure.microsoft.com/support/create-ticket/) .

Bu sorunu daha hızlı *bir şekilde* atlayabilmeniz IÇIN [SDK CDN uç noktasını değiştirebilirsiniz](#change-the-cdn-endpoint).

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>JavaScript CDN Application Insights engellendi (Son Kullanıcı tarafından engellenen tarayıcı tarafından engellendi; yüklü engelleyici; kişisel güvenlik duvarı)

Son kullanıcılarınızın şunları olup olmadığını denetleyin:

- Bir tarayıcı eklentisi (genellikle bir ad, kötü amaçlı yazılım veya açılan pencere engelleyici biçimi) yüklendi.
- Tarayıcılarınızda veya proxy 'de CDN uç noktaları Application Insights engellenmiş (veya izin verilmez).
- SDK için CDN etki alanının engellenmesine neden olan bir güvenlik duvarı kuralı yapılandırıldı (veya DNS girişi çözümlenmeyecek).

Bu seçeneklerden herhangi birini yapılandırdıysanız, CDN uç noktalarına izin vermek için bunlarla çalışmanız (veya belge sağlamanız) gerekecektir.

Yüklemiş oldukları eklentiler ortak blok listesini kullanıyor olabilir. Böyle bir durum söz konusu değilse, büyük olasılıkla başka bir el ile yapılandırılmış çözüm veya özel bir etki alanı Blocklist kullanıyor olabilir.

#### <a name="add-exceptions-for-cdn-endpoints"></a>CDN uç noktaları için özel durum ekleme

Son kullanıcılarınızla çalışın veya Application Insights CDN uç noktalarından gelen betiklerin tarayıcının eklenti veya güvenlik duvarı kuralı özel durum listesine dahil edilmesi için bunlara izin vermesi gerektiğini bildiren bir belge sağlayın (son kullanıcının ortamına göre farklılık gösterir).

[Chrome 'un web sitelerine erişime izin vermek veya erişimi engellemek için](https://support.google.com/chrome/a/answer/7532419?hl=en) nasıl yapılandırılacağı hakkında bir örnek aşağıda verilmiştir.

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN engellendi (kurumsal güvenlik duvarı tarafından)

Son kullanıcılarınız bir kurumsal ağ üzerinde ise, büyük olasılıkla güvenlik duvarı çözümüdür ve BT departmanı bir Internet filtreleme sistemi formu uygulamıştır. Bu durumda, son kullanıcılarınız için gerekli kurallara izin vermek üzere bunlarla çalışmanız gerekir.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Şirketler için CDN uç noktaları için özel durumlar ekleyin

  Bu, [son kullanıcılar için özel durumlar eklemeye](#add-exceptions-for-cdn-endpoints)benzer, ancak bir etki alanı bloğunda veya izin verilenler listeleme hizmetlerine ekleyerek (veya kaldırarak) Application Insights CDN uç noktalarını yapılandırmak IÇIN şirketin BT departmanıyla birlikte çalışmanız gerekir.

  > [!WARNING]
  > Şirket kullanıcılarınız [özel bir bulut](https://azure.microsoft.com/overview/what-is-a-private-cloud/) kullanıyorsa ve kendi Iç kullanıcılarına CDN uç noktaları için ortak erişim sağlamak için herhangi bir özel durum biçimini etkinleştireiyorlarsa, [Application Insights NPM paketlerini](https://www.npmjs.com/package/applicationinsights) kullanmanız veya APPLICATION INSIGHTS SDK 'sını kendi CDN 'niz üzerinde barındırmanıza gerek duyarsınız.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>Engellenen CDN için ek sorun giderme

> [!NOTE]
> Kullanıcılarınız [özel bir bulut](https://azure.microsoft.com/overview/what-is-a-private-cloud/) kullanıyorsa ve ortak internet erişimi yoksa, SDK 'Yı kendi CDN 'niz üzerinde barındırmanıza veya NPM paketlerini kullanmanız gerekir.

#### <a name="host-the-sdk-on-your-own-cdn"></a>SDK 'sını kendi CDN 'niz üzerinde barındırın

 Son kullanıcılarınız Application Insights SDK 'sını genel CDN 'den indirmekle, Application Insights SDK 'sını kendi CDN uç noktanıza barındırabilmeniz gerekir. Hangi sürümü kullandığınızı daha kolay bir şekilde belirlemek için belirli bir sürüm (AI. 2. #. # .min.js) kullanmanız önerilir. Ayrıca, tüm hata düzeltmelerinden ve kullanılabilir yeni özelliklerden yararlanabilmeniz için, onu düzenli olarak geçerli sürüme (ai.2.min.js) güncelleştirin.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>Application Insight SDK 'sını eklemek için NPM paketlerini kullanma

Kod parçacığını ve genel CDN uç noktalarını kullanmak yerine, kendi JavaScript dosyalarınızın bir parçası olarak SDK 'Yı dahil etmek için [NPM paketlerini](https://www.npmjs.com/package/applicationinsights) kullanabilirsiniz. SDK yalnızca kendi betiklerinizde yer alan başka bir paket olur.

> [!NOTE]
> Kod bölme ve küçültmeye yardımcı olması için NPM paketleri kullanılırken bir [JavaScript paketcisi](https://www.bing.com/search?q=javascript+bundler) biçimi de kullanmanız önerilir.

Kod parçacığında olduğu gibi, kendi betiklerinizin (SDK NPM paketlerini kullanarak veya kullanmadan) burada listelenen engelleme sorunlarından etkilenmesinin yanı sıra, uygulamanıza, kullanıcılarınıza ve çerçevesine bağlı olarak, bu sorunları tespit etmek ve raporlamak için kod parçacığındaki mantığa benzer bir şeyler uygulamayı düşünmek isteyebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar 
- [GitHub 'da sorun kaydederek ek yardım alın](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Web sayfası kullanımını izle](javascript.md)

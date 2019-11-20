---
title: Web uygulaması performans izleme-Azure Application Insights | Microsoft Docs
description: Application Insights devOps döngüsüne nasıl uyar
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/21/2018
ms.openlocfilehash: bf7c0b4db2b7eb662f55b917dbe318d0ad8023ce
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677302"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Application Insights ile ayrıntılı web uygulaması ve hizmet tanılama
## <a name="why-do-i-need-application-insights"></a>Neden Application Insights gerekir?
Application Insights çalışan Web uygulamanızı izler. Hataları ve performans sorunlarını size bildirir ve müşterilerin uygulamanızı nasıl kullandığını analiz etmenize yardımcı olur. Birçok platformda (ASP.NET, Java EE, Node. js,...) çalışan uygulamalar için çalışır ve bulutta ya da şirket içinde barındırılır. 

![Web uygulamaları sunma karmaşıklığının yönleri](./media/devops/010.png)

Bir modern uygulamayı çalışırken izlemek gereklidir. En önemlisi, müşterilerinizden büyük bir kısmını yapmadan önce başarısız olan sorunları tespit etmek istiyorsunuz. Ayrıca, çok önemli olmayan, belki de yavaş olan veya kullanıcılarınız için bazı zorluk gösteren performans sorunlarını bulup gidermek istersiniz. Ayrıca sistem memnuniyet düzeyini gerçekleştirirken, kullanıcıların onunla ne yaptığını bilmeniz gerekir: en son özelliği kullanıyor musunuz? Bunlar ile başarılı oluyor mu?

Modern Web uygulamaları sürekli teslim döngüsüyle geliştirilmiştir: yeni bir özellik veya geliştirme sürümü yayınlama; kullanıcılar için ne kadar iyi çalışma olduğunu gözlemleyin; Bu bilgiye göre yeni geliştirme artışını planlayın. Bu döngüsünün önemli bir bölümü gözlemleyici aşamasıdır. Application Insights, bir Web uygulamasını performans ve kullanım için izlemeye yönelik araçlar sağlar.

Bu işlemin en önemli yönü tanılama ve tanılamadır. Uygulama başarısız olursa, iş kaybedilmekte olur. Bu nedenle, bir izleme çerçevesinin ana rolü, sorunları güvenilir bir şekilde algılayıp sizi hemen bilgilendirmesini ve sorunu tanılamak için gereken bilgileri sunmanızı sağlar. Bu tam olarak Application Insights.

### <a name="where-do-bugs-come-from"></a>Hatalar nereden geliyor?
Web sistemlerindeki başarısızlıklar genellikle yapılandırma sorunlarından veya birçok bileşeni arasındaki hatalı etkileşimlerden oluşur. Bu nedenle canlı bir site olayına neden olan ilk görev, sorunun Locus 'sini belirlemektir: nedeni hangi bileşen veya ilişki olabilir?

Gri saç olan bazı ABD, bir bilgisayar programının bir bilgisayarda çalıştığı daha basit bir dönemi anımsayabilir. Geliştiriciler, göndermeden önce onu tamamen test edecektir; ve sevk edildiğinde, bu dosyayı nadiren görebilir veya bir kez daha düşünebilir. Kullanıcıların birçok yıl boyunca fazlalık hataları olması gerekir. 

Artık çok farklı şeyler. Uygulamanızın üzerinde çalışması için bir plethora farklı cihaz vardır ve her birinde aynı davranışın tam olarak garanti edilmesi zor olabilir. Bulutta barındırma uygulamalar, hataların hızlı bir şekilde düzeltibileceği anlamına gelir, ancak aynı zamanda sürekli yarışma ve yeni özelliklerin beklentilerinin sık aralıklarla beklendiği anlamına gelir. 

Bu koşullarda, hata sayısı üzerinde bir kesin denetimi tutmanın tek yolu otomatik birim sınamadır. Her teslimdeki her şeyin el ile yeniden test edilmesi imkansız olabilir. Birim testi artık yapı sürecinin normal kısmıdır. Birden çok tarayıcı sürümünde otomatik UI testi sağlayarak Xamarin Test Cloud yardım gibi araçlar. Bu test Regimes, bir uygulama içinde bulunan hataların oranının en düşük düzeyde tutulabilmesine imkan sağlar.

Tipik Web uygulamalarında birçok canlı bileşen vardır. İstemciye (bir tarayıcı veya cihaz uygulamasında) ve Web sunucusuna ek olarak, büyük ölçüde arka uç işleme olabilir. Arka uç, bileşenlerin bir işlem hattından veya işbirliği parçalarının bir gevleci koleksiyonudur. Ve bunların birçoğu, sizin deneti, bağlı olduğunuz dış hizmetlerdir.

Bunlar gibi yapılandırmalarda, her olası hata modunun, canlı sistemin kendisi dışında, test edilmesi veya öngörülebilir olması zor ve ekonomik olabilir. 

### <a name="questions-"></a>Sorular...
Bir Web sistemi geliştirilirken sorduğumuz bazı sorular:

* Uygulamamın kilitlenme midir? 
* Ne tam olarak gerçekleşti? -Bir istek başarısız olursa, nasıl olduğunu öğrenmek istiyorum. Olayların izlenmesi gerekiyor...
* Uygulamam yeterince hızlı mı? Tipik isteklere yanıt vermek ne kadar sürer?
* Sunucu yükü işleyebilir mi? İstek hızı, yanıt süresi sürekli olarak tutuluyor mu?
* Bağımlılıklarım ne kadar çabuk? REST API 'Leri, veritabanları ve uygulamamın çağırdığı diğer bileşenler. Özellikle, sistem yavaşsa, bileşenim mi yoksa başkasından daha yavaş yanıtlar alıyorum?
* Uygulamam çalışıyor veya çalışmıyor mu? Dünyanın dört bir yanındaki görünebilir mi? Yanıt vermeyi durdurduğunu söyleyin....
* Kök nedeni nedir? Bileşenimde veya bir bağımlılığın hatası mı var? Bu bir iletişim sorunu mı var?
* Kaç Kullanıcı etkilendi? En önemli olan, tacyü birden fazla sorun yaşıyorum mi?

## <a name="what-is-application-insights"></a>Application Insights nedir?
![Application Insights temel iş akışı](./media/devops/020.png)

1. Uygulamanızı araçlar Application Insights ve uygulama çalışırken BT hakkında telemetri gönderir. Application Insights SDK 'Yı uygulamaya oluşturabilir ya da çalışma zamanında izleme uygulayabilirsiniz. Normal modüllere kendi telemetrinizi ekleyebileceğiniz gibi, önceki yöntem daha esnektir.
2. Telemetri, depolandığı ve işlendiği Application Insights portalına gönderilir. (Application Insights Microsoft Azure barındırmakla birlikte, yalnızca Azure uygulamalarını değil, tüm Web uygulamalarını izleyebilir.)
3. Telemetri, grafik ve olay tabloları biçiminde size sunulur.

İki temel telemetri türü vardır: toplanmış ve ham örnekler. 

* Örnek veriler, örneğin, Web uygulamanız tarafından alınmış bir isteğin raporunu içerir. Application Insights portalındaki arama aracını kullanarak bir isteğin ayrıntılarını bulabilir ve inceleyebilirsiniz. Örnek, uygulamanızın isteğe ne kadar süre yanıt verdiğini, istenen URL 'yi, istemcinin yaklaşık konumunu ve diğer verileri de içerir.
* Toplanan veriler, yanıt süreleriyle isteklerin oranını karşılaştırabilmeniz için birim zamanına göre olayların sayısını içerir. Ayrıca, istek yanıt süreleri gibi ölçümlerin ortalamaları da dahildir.

Verilerin ana kategorileri şunlardır:

* Uygulamanıza yönelik istekler (genellikle HTTP istekleri), URL, yanıt süresi ve başarı veya başarısızlık hakkındaki verilerle.
* Bağımlılıklar-uygulamanızın yaptığı REST ve SQL çağrıları, ayrıca URI, yanıt süreleri ve başarı
* Yığın izlemeleri dahil özel durumlar.
* Kullanıcıların tarayıcılarından gelen sayfa görüntüleme verileri.
* Performans sayaçları, ayrıca yazdığınız ölçümler gibi ölçümler. 
* İş olaylarını izlemek için kullanabileceğiniz özel olaylar
* Hata ayıklama için kullanılan günlük izlemeleri.

## <a name="case-study-real-madrid-fc"></a>Örnek olay Incelemesi: Real Madrid F.C.
[Gerçek Madrid futbol Kulükünün](https://www.realmadrid.com/) Web hizmeti dünyanın dört bir yanındaki 450.000.000 fanın üzerinde hizmet verir. Fanlar, Web tarayıcıları ve kulübünün mobil uygulamaları aracılığıyla her ikisine de erişir. Fanı yalnızca bilet defteridir, ancak sonuçlar, oyuncular ve yaklaşan oyunlarda bilgilere ve video kliplerine da erişemez. Bunlar, puanların sayısı gibi filtrelerle arama yapabilir. Sosyal medya bağlantıları da vardır. Kullanıcı deneyimi son derece kişiselleştirilir ve fanları sağlamak için iki yönlü bir iletişim olarak tasarlanmıştır.

Çözüm, [Microsoft Azure bir hizmet ve uygulama sistemidir](https://www.microsoft.com/inculture/sports/real-madrid/). Ölçeklenebilirlik önemli bir gereksinimdir: trafik değişkendir ve eşleşmeler sırasında ve sonrasında çok yüksek birimlere ulaşabilir.

Gerçek Madrid için sistemin performansını izlemek çok önemlidir. Azure Application Insights, sistem genelinde, güvenilir ve yüksek düzeyde bir hizmet sunan kapsamlı bir görünüm sağlar. 

Kulüler Ayrıca, fanlarının derinlemesine olarak anlaşılmasına neden olur: nerede (yalnızca %3 ' e ait), oynatıcılarda hangi ilgi, geçmiş sonuçları ve yaklaşan oyunları ve sonuçları eşleştirmek için nasıl yanıt vereceğini öğrenin.

Bu Telemetri verilerinin çoğu, çözümü basitleşen ve daha az işlemsel karmaşıklık sağlayan eklenen kod olmadan otomatik olarak toplanır.  Gerçek Madrid için, her ay 3.800.000.000 telemetri noktasıyla anlaşmalar Application Insights.

Gerçek Madrid, telemetrisini görüntülemek için Power BI modülünü kullanır.

![Application Insights telemetrinin Power BI görünümü](./media/devops/080.png)

## <a name="smart-detection"></a>Akıllı algılama
[Proaktif Tanılamalar](../../azure-monitor/app/proactive-diagnostics.md) son bir özelliktir. Sizin tarafınızdan özel bir yapılandırma olmadan, Application Insights uygulamanızdaki hata tarifelerinde olağan dışı bir şekilde otomatik olarak algılanır ve sizi uyarır. Zaman zaman oluşan hataların bir arka planını yok saymaya ve ayrıca yalnızca müşterinizin istekleriyle orantılı istekleri için de tek bir deyişle bu kadar akıllı bir değer. Örneğin, bağlı olduğunuz hizmetlerden birinde bir hata varsa veya yeni dağıttığınız derleme bu şekilde çalışmıyorsa, e-postanıza baktığımızda onu öğrenirsiniz. (Ve diğer uygulamaları tetikleyebilmeniz için Web kancaları vardır.)

Bu özelliğin başka bir yönü, telemetrinizin günlük derinlemesine bir analizini gerçekleştirerek keşfedilecek olağan dışı bir performans düzeni sağlar. Örneğin, belirli bir coğrafi alanla veya belirli bir tarayıcı sürümüyle ilişkili yavaş performansı bulabilir.

Her iki durumda da, uyarı yalnızca bunun bulunduğu belirtileri söylemmekle kalmaz, ilgili özel durum raporları gibi sorunu tanılamanıza yardımcı olması için size gereken verileri de sağlar.

![Proaktif tanılama 'dan e-posta](./media/devops/030.png)

Customer Samtec: "yeni bir özellik cutover sırasında, kaynak sınırlarına vurduğu ve zaman aşımına neden olan, ölçeği belirtilmiş bir veritabanı bulduk. Proaktif algılama uyarıları, büyük olasılıkla gerçek zamanlı olarak tanıtılan şekilde çok büyük bir süre içinde olduğu gibi, tam olarak bir sorunla karşılaştık. Bu uyarı, Azure platform uyarıları ile birlikte kullanıldığında sorunu neredeyse anında çözmemize yardımcı oldu. 10 dakikalık Toplam kesinti süresi <. "

## <a name="live-metrics-stream"></a>Canlı Ölçüm Akışı
En son derlemeyi dağıtmak, anormal bir deneyim olabilir. Herhangi bir sorun varsa, bu sorunları hemen öğrenmek istersiniz. böylece, gerekirse bunları geri alabilirsiniz. Canlı Ölçüm Akışı, bir saniyelik bir gecikme süresi ile anahtar ölçümleri sağlar.

![Canlı ölçümler](./media/devops/0040.png)

Ve tüm hataların veya özel durumların bir örneğini hemen incelemenizi sağlar.

![Canlı hata olayları](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Uygulama Eşlemesi
Uygulama eşleme, dağıtılmış ortamınızdaki performans sorunlarını ve sorunlu akışları kolayca tanımlamanızı sağlamak için, uygulama topolojinizi otomatik olarak bulur ve bunun üzerine performans bilgilerini kolayca belirlemenizi sağlar. Azure hizmetlerinde uygulama bağımlılıklarını keşfetmenize olanak sağlar. Sorunu, kod ile ilgili veya bağımlılık olup olmadığını ve tek bir yerden ilgili tanılama deneyimlerine gitmeyi anlamak için önceliklendirin. Örneğin, uygulamanız SQL katmanında performans düşüşü nedeniyle başarısız olabilir. Uygulama haritası ile, hemen görebilir ve SQL dizin danışmanı veya sorgu öngörüleri deneyimini ayrıntılı olarak inceleyebilirsiniz.

![Uygulama Eşlemesi](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Application Insights Analizi
[Analiz](../../azure-monitor/app/analytics.md)sayesinde, güçlü bir SQL benzeri dilde rastgele sorgular yazabilirsiniz.  Birçok perspektifin birbirine bağlanması, tüm uygulama yığınının tamamında tanılanması kolaydır ve hizmet performansını Iş ölçümleri ve müşteri deneyimiyle ilişkilendirmek için doğru soruları sorabilirsiniz. 

Portalda depolanan tüm telemetri örneğinizi ve ölçüm ham verilerini sorgulayabilirsiniz. Dil, filtre, birleştirme, toplama ve diğer işlemleri içerir. Alanları hesaplayabilir ve istatistiksel çözümlemeler yapabilirsiniz. Tablosal ve grafik görselleştirmeler vardır.

![Analiz sorgusu ve sonuç grafiği](./media/devops/0025.png)

Örneğin, şunları yapmak kolaydır:

* Deneyimlerini anlamak için uygulamanızın istek performansı verilerini müşteri katmanlarına göre segmentlere ayırın.
* Canlı site araştırmalar sırasında belirli hata kodlarını veya özel olay adlarını arayın.
* Özelliklerin nasıl elde edildiğini ve benimsenileceğini anlamak için belirli müşterilerin uygulama kullanımının ayrıntılarına gidin.
* Belirli kullanıcıların destek ve operasyon ekiplerini hızlı müşteri desteği sağlamasına olanak tanımak için oturumları ve yanıt sürelerini izleyin.
* Özellik önceliği belirleme sorularına yanıt vermek için sık kullanılan uygulama özelliklerini belirleme.

Müşteri DNN diyor ki: "Application Insights, verileri gereken şekilde birleştirebilmek, sıralamak, sorgulamak ve filtrelemek için denklemin eksik bir bölümünü sağladı. Ekibimizin güçlü bir sorgu dili ile veri bulmak için kendi uyumluluk ve deneyimlerini kullanmasına izin vermek, bizim de bize ait olduğumuz anlayışımız sorunları bulmamıza ve çözmemize olanak tanıyor. *' I merak ediyor... '* ile başlayan sorulardan çok sayıda ilginç yanıt gelir.

## <a name="development-tools-integration"></a>Geliştirme araçları tümleştirmesi
### <a name="configuring-application-insights"></a>Application Insights yapılandırma
Visual Studio ve tutulma, geliştirmekte olduğunuz proje için doğru SDK paketlerini yapılandırmak için araçlara sahiptir. Application Insights eklemek için bir menü komutu vardır.

Log4N, NLog veya System. Diagnostics. Trace gibi bir izleme günlüğü çatısı kullanıyorsanız, bunları diğer telemetriyle birlikte Application Insights gönderme seçeneğine sahip olursunuz. bu sayede, izlemeleri isteklerle, bağımlılığıyla kolayca ilişkilendirebilmenizi sağlayabilirsiniz. çağrılar ve özel durumlar.

### <a name="search-telemetry-in-visual-studio"></a>Visual Studio 'da telemetri arama
Bir özelliği geliştirirken ve hata ayıklarken, Web portalındaki ile aynı arama tesislerini kullanarak Telemetriyi doğrudan Visual Studio 'da görüntüleyebilir ve arayabilirsiniz.

Application Insights bir özel durum günlüğe kaydederken, Visual Studio 'da veri noktasını görüntüleyebilir ve ilgili koda doğrudan atlayabilirsiniz.

![Visual Studio arama](./media/devops/060.png)

Hata ayıklama sırasında, Telemetriyi geliştirme makinenizde tutma, Visual Studio 'da görüntüleme, ancak portala gönderme seçeneğiniz vardır. Bu yerel seçenek, hata ayıklamayı üretim telemetrisi ile karıştırmaya karşı önler.

### <a name="work-items"></a>İş öğeleri
Bir uyarı ortaya çıktığında Application Insights, iş izleme sisteminizde otomatik olarak bir iş öğesi oluşturabilir.

## <a name="but-what-about"></a>Ancak...?
* [Gizlilik ve depolama](../../azure-monitor/app/data-retention-privacy.md) -Azure güvenli sunucularında telemetri tutulur.
* Performans-etki çok düşüktür. Telemetri toplu olarak oluşturulur.
* [Fiyatlandırma](../../azure-monitor/app/pricing.md) -ücretsiz olarak kullanmaya başlayabilir ve düşük hacimde devam edersiniz.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Sonraki adımlar
Application Insights kullanmaya başlamak kolaydır. Ana Seçenekler şunlardır:

* [Azure App Service](../../azure-monitor/app/app-insights-overview.md)Için de [IIS sunucuları](../../azure-monitor/app/monitor-performance-live-website-now.md).
* Geliştirme sırasında projenizi işaretleyin. Bunu [ASP.net](../../azure-monitor/app/asp-net.md) veya [Java](../../azure-monitor/app/java-get-started.md) uygulamaları için, ayrıca [Node. js](../../azure-monitor/app/nodejs.md) ve [diğer türlerde](../../azure-monitor/app/platforms.md)bir konak için yapabilirsiniz. 
* Kısa bir kod parçacığı ekleyerek [herhangi bir Web sayfasını](../../azure-monitor/app/javascript.md) işaretleyin.


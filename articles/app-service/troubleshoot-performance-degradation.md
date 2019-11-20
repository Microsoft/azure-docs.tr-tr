---
title: Performans düşüşü sorunlarını giderme-Azure App Service | Microsoft Docs
description: Bu makale, Azure App Service ' deki yavaş uygulama performansı sorunlarını gidermenize yardımcı olur.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: Web uygulaması performansı, yavaş uygulama, uygulama yavaş
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 3f7389022eaee4268d5d4fc5439b64d7f7f1bf07
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066537"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Azure App Service 'de yavaş uygulama performansı sorunlarını giderme
Bu makale, [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)' deki yavaş uygulama performansı sorunlarını gidermenize yardımcı olur.

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**'a tıklayın.

## <a name="symptom"></a>Belirti
Uygulamaya gözatarken Sayfalar yavaş yüklenir ve bazen zaman aşımına uğramaktadır.

## <a name="cause"></a>Nedeni
Bu sorun genellikle uygulama düzeyi sorunlarından kaynaklanır, örneğin:

* uzun zaman alan ağ istekleri
* uygulama kodu veya veritabanı sorguları verimsiz
* yüksek bellek/CPU kullanan uygulama
* uygulama bir özel durum nedeniyle çökme

## <a name="troubleshooting-steps"></a>Sorun giderme adımları
Sorun giderme üç farklı göreve sıralı sırayla ayrılabilir:

1. [Uygulama davranışını gözlemleyin ve izleyin](#observe)
2. [Veri topla](#collect)
3. [Sorunu azaltma](#mitigate)

[App Service](overview.md) her adımda çeşitli seçenekler sunar.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Uygulama davranışını gözlemleyin ve izleyin
#### <a name="track-service-health"></a>Hizmet durumunu izleme
Her bir hizmet kesintisi veya performans düşüşü olduğunda Microsoft Azure publicleştirir. [Azure Portal](https://portal.azure.com/)hizmetin sistem durumunu izleyebilirsiniz. Daha fazla bilgi için bkz. [hizmet durumunu izleme](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Uygulamanızı izleme
Bu seçenek, uygulamanızda herhangi bir sorun olup olmadığını bulmanıza olanak sağlar. Uygulamanızın dikey penceresinde **İstekler ve hatalar** kutucuğuna tıklayın. **Ölçüm** dikey penceresi, ekleyebileceğiniz tüm ölçümleri gösterir.

Uygulamanız için izlemek isteyebileceğiniz bazı ölçümler şunlardır

* Ortalama bellek çalışma kümesi
* Ortalama yanıt süresi
* CPU süresi
* Bellek çalışma kümesi
* İstekler

![uygulama performansını izleme](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Daha fazla bilgi için bkz.

* [Azure App Service uygulamaları izleme](web-sites-monitor.md)
* [Uyarı bildirimleri alma](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Web uç noktası durumunu izleme
Uygulamanızı **Standart** fiyatlandırma katmanında çalıştırıyorsanız, App Service üç coğrafi konumdan iki uç noktayı izlemenizi sağlar.

Uç nokta izleme, Web URL 'lerinin yanıt süresini ve çalışma süresini test eden coğrafi olarak dağıtılmış konumlardan Web testlerini yapılandırır. Test, her konumdan yanıt süresini ve çalışma süresini belirleyebilmek için Web URL 'sinde bir HTTP GET işlemi gerçekleştirir. Her yapılandırılmış konum her beş dakikada bir test çalıştırır.

Çalışma süresi HTTP yanıt kodları kullanılarak izlenir ve yanıt süresi milisaniye cinsinden ölçülür. HTTP yanıt kodu 400 değerinden büyük veya bu değere eşitse veya Yanıt 30 saniyeden uzun sürerse izleme testi başarısız olur. İzleme sınamaları belirtilen konumların tümünde başarılı olursa bir uç nokta kullanılabilir olarak kabul edilir.

Ayarlamak için, bkz. [Azure App Service uygulamaları izleme](web-sites-monitor.md).

Ayrıca bkz. Azure Web sitelerini ve uç nokta Izlemeyi, uç nokta izlemeye yönelik bir video için [Stefan Schackow Ile koruma](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) .

#### <a name="application-performance-monitoring-using-extensions"></a>Uzantıları kullanarak uygulama performansı izleme
Ayrıca, bir *site uzantısı*kullanarak uygulama performansınızı izleyebilirsiniz.

Her App Service uygulaması, site uzantıları olarak dağıtılan güçlü bir araç kümesini kullanmanıza olanak sağlayan genişletilebilir bir yönetim uç noktası sağlar. Uzantılar şunları içerir: 

- [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx)gibi kaynak kodu düzenleyicileri. 
- Bir uygulamaya bağlı MySQL veritabanı gibi bağlı kaynaklar için yönetim araçları.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) , aynı zamanda kullanılabilen bir performans izleme site uzantısıdır. Application Insights kullanmak için, kodunuzu bir SDK ile yeniden derleyin. Ek verilere erişim sağlayan bir uzantı da yükleyebilirsiniz. SDK, uygulamanızın kullanımını ve performansını daha ayrıntılı olarak izlemek için kod yazmanıza olanak tanır. Daha fazla bilgi için bkz. [Web uygulamalarında performansı izleme](../azure-monitor/app/web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Veri toplama
App Service, hem Web sunucusundan hem de Web uygulamasından günlüğe bilgi günlüğü için tanılama işlevselliği sağlar. Bilgiler, Web sunucusu tanılama ve Uygulama Tanılama ile ayrılmıştır.

#### <a name="enable-web-server-diagnostics"></a>Web sunucusu tanılamayı etkinleştir
Aşağıdaki türlerde günlükleri etkinleştirebilir veya devre dışı bırakabilirsiniz:

* **Ayrıntılı hata günlüğü** -http durum kodları için hata belirten ayrıntılı hata bilgileri (durum kodu 400 veya üzeri). Bu, sunucunun neden hata kodunu döndürmediğini belirlemede yardımcı olabilecek bilgiler içerebilir.
* **Başarısız Istek izleme** -isteği işlemek IÇIN kullanılan IIS bileşenlerinin izlenmesi ve her bileşende geçen süre dahil olmak üzere başarısız istekler hakkında ayrıntılı bilgiler. Bu, uygulama performansını arttırmaya veya belirli bir HTTP hatasına neden olan hataları ayırmaya çalışıyorsanız yararlı olabilir.
* **Web sunucusu günlüğü** -W3C Genişletilmiş günlük dosyası BIÇIMINI kullanarak http işlemleri hakkında bilgiler. Bu, işlenen istek sayısı veya belirli bir IP adresinden kaç istek olduğu gibi genel uygulama ölçümlerinin saptanmasında yararlıdır.

#### <a name="enable-application-diagnostics"></a>Uygulama tanılamayı etkinleştir
App Service 'den uygulama performans verilerini toplamak, uygulamanızın Visual Studio 'dan canlı olarak profilini oluşturup daha fazla bilgi ve izlemeleri günlüğe kaydetmek için uygulama kodunuzu değiştirmek için çeşitli seçenekler vardır. Uygulamaya sahip olduğunuz erişime ve izleme araçlarından gözlemlerinizi temel alan seçenekleri belirleyebilirsiniz.

##### <a name="use-application-insights-profiler"></a>Application Insights Profiler kullan
Application Insights Profiler, ayrıntılı performans izlemelerini yakalamaya başlamak için etkinleştirebilirsiniz. Geçmişte oluşan sorunları araştırmanız gerektiğinde, beş güne kadar yakalanan izlemelere erişebilirsiniz. Azure portal üzerindeki uygulamanın Application Insights kaynağına erişiminiz olduğu sürece bu seçeneği belirleyebilirsiniz.

Application Insights Profiler, her bir Web çağrısının yanıt süresine ilişkin istatistikleri ve hangi kod satırının yavaş yanıtlara neden olduğunu gösteren izlemeleri sağlar. Bazı durumlarda, bazı kodlar performanslı bir şekilde yazılmadığından App Service uygulama yavaş olur. Örnek olarak, paralel ve istenmeyen veritabanı kilitleme çekişmeleri içinde çalıştırılabilen sıralı kod verilebilir. Koddaki bu engelleri kaldırma, uygulamanın performansını artırır, ancak ayrıntılı izlemeler ve Günlükler ayarlamadan tespit etmek zordur. Application Insights Profiler tarafından toplanan izlemeler, uygulamayı yavaşlatan ve App Service uygulamalar için bu zorluk ortadan kaldırarak kod satırlarını tanımlamaya yardımcı olur.

 Daha fazla bilgi için bkz. [Application Insights ile Azure App Service 'da canlı uygulamalar profili oluşturma](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Uzaktan profil oluşturma kullan
Azure App Service, Web Apps, API Apps, mobil arka uçlar ve WebJobs uygulamalarında uzaktan profil oluşturulabilir. Uygulama kaynağına erişiminiz varsa ve sorunu nasıl yeniden üreteceğimizi ya da performans sorununun tam zaman aralığını biliyorsanız bu seçeneği belirleyin.

Uzaktan profil oluşturma işlemi, işlemin CPU kullanımı yüksekse ve işleminiz beklenenden yavaş çalışıyorsa veya HTTP isteklerinin gecikmesi normalden fazlaysa, işleminizi uzaktan oluşturup işlem çözümlemek için CPU örnekleme çağrı yığınlarını alabilirsiniz etkinlik ve kod etkin yolları.

Daha fazla bilgi için bkz. [Azure App Service uzaktan profil oluşturma desteği](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Tanılama izlemelerini el ile ayarlama
Web uygulaması kaynak koduna erişiminiz varsa, Application Diagnostics bir Web uygulaması tarafından üretilen bilgileri yakalamanızı sağlar. ASP.NET uygulamaları, uygulama tanılama `System.Diagnostics.Trace` günlüğüne bilgi kaydetmek için sınıfını kullanabilir. Ancak, kodu değiştirmeniz ve uygulamanızı yeniden dağıtmanız gerekir. Uygulamanız bir sınama ortamında çalışıyorsa bu yöntem önerilir.

Uygulamanızı günlüğe kaydetmeye yönelik yapılandırma hakkında ayrıntılı yönergeler için, bkz. [Azure App Service uygulamalar için tanılama günlüğünü etkinleştirme](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Tanılama aracını kullanma
App Service, yapılandırma gerekmeden uygulamanızdaki sorunları gidermenize yardımcı olacak akıllı ve etkileşimli bir deneyim sağlar. Uygulamanız ile ilgili sorunlar yaşıyorsanız, Tanılama Aracı, sorunu daha kolay ve hızlı bir şekilde gidermeye ve çözmeye yönelik doğru bilgilere kılavuzluk etmek için nelerin yanlış olduğunu gösterir.

App Service tanılama 'ya erişmek için App Service uygulamanıza veya [Azure portal](https://portal.azure.com)App Service ortamı gidin. Sol gezinti bölmesinde, **sorunları Tanıla ve çöz**' e tıklayın.

#### <a name="use-the-kudu-debug-console"></a>Kudu hata ayıklama konsolunu kullanma
App Service, hata ayıklama, keşfetme, yükleme dosyaları ve ortamınız hakkında bilgi almak için JSON uç noktaları için kullanabileceğiniz bir hata ayıklama konsoluyla birlikte gelir. Bu konsola *kudu konsolu* veya uygulamanız Için *SCM panosu* adı verilir.

Bu panoya, **>. scm. azurewebsites. net/&lt;adlı uygulamanızın adını https://** bağlantısına giderek erişebilirsiniz.

Kudu 'nin sağladığı işlemlerden bazıları şunlardır:

* uygulamanız için ortam ayarları
* günlük akışı
* Tanılama dökümü
* PowerShell cmdlet 'lerini ve temel DOS komutlarını çalıştırabileceğiniz hata ayıklama konsolu.

Kudu 'nin başka bir faydalı özelliği de, uygulamanızın ilk şans özel durumlarını oluşturması durumunda kudu ve SysInternals aracı ProcDump ' i kullanarak bellek dökümleri oluşturabilir. Bu bellek dökümleri işlemin anlık görüntüleridir ve genellikle uygulamanızla ilgili karmaşık sorunları gidermenize yardımcı olabilir.

Kudu 'de kullanılabilen özellikler hakkında daha fazla bilgi için, bkz. [bilmeniz gereken Azure DevOps araçları](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Sorunu azaltma
#### <a name="scale-the-app"></a>Uygulamayı ölçeklendirme
Azure App Service, daha fazla performans ve verimlilik için uygulamanızı çalıştırdığınız ölçeği ayarlayabilirsiniz. Bir uygulamanın ölçeğini değiştirmek, iki ilgili eylemi içerir: App Service planınızı daha yüksek bir fiyatlandırma katmanına değiştirme ve belirli ayarları daha yüksek fiyatlandırma katmanına geçtikten sonra yapılandırma.

Ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure App Service bir uygulamayı ölçeklendirme](manage-scale-up.md).

Ayrıca, uygulamanızı birden fazla örnekte çalıştırmayı seçebilirsiniz. Genişletme yalnızca daha fazla işleme özelliği sağlamaz, ancak size bazı hata toleransı sağlar. İşlem bir örnekte daha fazla olursa, diğer örnekler istekleri sunmaya devam eder.

Ölçeklendirmeyi El Ile veya otomatik olarak ayarlayabilirsiniz.

#### <a name="use-autoheal"></a>Oto Heal kullanma
Oto Heal, seçtiğiniz ayarlara göre (yapılandırma değişiklikleri, istekler, bellek tabanlı sınırlar veya bir isteği yürütmek için gereken süre gibi) uygulamanızın çalışan işlemini geri dönüştürür. Çoğu zaman, bir sorunu kurtarmanın en hızlı yoludur. Uygulamayı her zaman doğrudan Azure portal içinden yeniden başlatabilseniz de, Otomatik Heal bunu sizin için otomatik olarak yapar. Yapmanız gereken tek şey, uygulamanız için kök Web. config dosyasında bazı Tetikleyiciler eklemektir. Bu ayarlar, uygulamanız .NET uygulaması olmasa bile aynı şekilde çalışır.

Daha fazla bilgi için bkz. [Otomatik Düzeltme Azure Web siteleri](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Uygulamayı yeniden başlatma
Yeniden başlatma, genellikle tek seferlik sorunlardan kurtulmanın en kolay yoludur. [Azure Portal](https://portal.azure.com/)uygulamanızın dikey penceresinde uygulamanızı durdurma veya yeniden başlatma seçenekleriniz vardır.

 ![performans sorunlarını çözmek için uygulamayı yeniden başlatın](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Uygulamanızı Azure PowerShell kullanarak da yönetebilirsiniz. Daha fazla bilgi için bkz. [Azure PowerShell'i Azure Resource Manager ile kullanma](../powershell-azure-resource-manager.md).

---
title: Uygulama performansı SSS-Azure App Service | Microsoft Docs
description: Azure App Service Web Apps özelliğindeki kullanılabilirlik, performans ve uygulama sorunları hakkında sık sorulan soruların yanıtlarını alın.
services: app-service\web
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 9cd529424a022edfa64f9053a53cbbe9f756637e
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122062"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Azure 'da Web Apps için uygulama performansı SSS

> [!NOTE]
> Aşağıdaki yönergelerin bazıları yalnızca Windows veya Linux uygulama hizmetlerinde çalışabilir. Örneğin, Linux uygulama hizmetleri varsayılan olarak 64 bitlik modda çalışır.
>

Bu makalede, [Azure App Service Web Apps özelliği](https://azure.microsoft.com/services/app-service/web/)için uygulama performansı sorunları hakkında sık sorulan soruların (SSS) yanıtları bulunur.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Uygulamam neden yavaş?

Yavaş uygulama performansına birçok etken katkıda bulunabilir. Ayrıntılı sorun giderme adımları için bkz. [yavaş Web uygulaması performansına sorun giderme](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Yüksek CPU tüketim senaryosunda sorun Nasıl yaparım? mi?

Bazı yüksek CPU tüketimi senaryolarında, uygulamanız gerçekten daha fazla bilgi işlem kaynağı gerektirebilir. Bu durumda, uygulamanın ihtiyaç duyacağı tüm kaynakları alması için daha yüksek bir hizmet katmanına ölçeklendirmeyi değerlendirin. Diğer zamanlarda, yüksek CPU tüketimine hatalı bir döngü veya bir kodlama uygulaması neden olmuş olabilir. Artan CPU tüketiminin tetiklenmesi hakkında öngörüler elde etmek iki bölümden oluşan bir işlemdir. İlk olarak, bir işlem dökümü oluşturun ve ardından işlem dökümünü çözümleyin. Daha fazla bilgi için bkz. [Web Apps için yüksek CPU tüketimi için döküm dosyası yakalama ve çözümleme](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Yüksek bellek tüketim senaryosunda sorun Nasıl yaparım?.

Bazı yüksek bellek tüketim senaryolarında, uygulamanız gerçekten daha fazla bilgi işlem kaynağı gerektirebilir. Bu durumda, uygulamanın ihtiyaç duyacağı tüm kaynakları alması için daha yüksek bir hizmet katmanına ölçeklendirmeyi değerlendirin. Diğer zamanlarda, koddaki bir hata bellek sızıntısına neden olabilir. Kodlama uygulaması da bellek tüketimini artırabilir. Yüksek bellek tüketiminin tetiklenmesi hakkında öngörü almak iki bölümden oluşan bir işlemdir. İlk olarak, bir işlem dökümü oluşturun ve ardından işlem dökümünü çözümleyin. Azure Site uzantısı galerisinden kilitlenme tanılayıcı, her iki adımı da etkili bir şekilde gerçekleştirebilir. Daha fazla bilgi için bkz. [Web Apps için aralıklı yüksek bellek için döküm dosyası yakalama ve çözümleme](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>PowerShell kullanarak App Service Web uygulamalarını otomatikleştirin Nasıl yaparım??

App Service Web uygulamalarını yönetmek ve korumak için PowerShell cmdlet 'lerini kullanabilirsiniz. Blog gönderimizde, [PowerShell kullanarak Azure App Service içinde barındırılan Web uygulamalarını otomatikleştirin](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), ortak görevleri otomatikleştirmek için Azure Resource Manager tabanlı PowerShell cmdlet 'lerinin nasıl kullanılacağını anlatmaktadır. Blog gönderisinin ayrıca çeşitli Web Apps yönetim görevleri için örnek kodları vardır. Tüm App Service Web Apps cmdlet 'lerinin açıklamaları ve sözdizimi için bkz. [az. Web siteleri](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Web uygulamamın olay Günlüklerimi görüntülemek Nasıl yaparım? mı?

Web uygulamanızın olay günlüklerini görüntülemek için:

1. [Kudu Web sitenizde](https://*yourwebsitename*.scm.azurewebsites.net)oturum açın.
2. Menüsünde, **Hata Ayıkla konsol** > **cmd**' yi seçin.
3. **LogFiles** klasörünü seçin.
4. Olay günlüklerini görüntülemek için **EventLog. xml**' nin yanındaki kurşun kalem simgesini seçin.
5. Günlükleri indirmek için PowerShell cmdlet 'ini `Save-AzureWebSiteLog -Name webappname`çalıştırın.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Web uygulamamın Kullanıcı modu bellek dökümünü Nasıl yaparım? yakalayın mi?

Web uygulamanızın kullanıcı modu bellek dökümünü yakalamak için:

1. [Kudu Web sitenizde](https://*yourwebsitename*.scm.azurewebsites.net)oturum açın.
2. **Işlem Gezgini** menüsünü seçin.
3. **W3wp. exe** Işlemini veya WebJob işleminizi sağ tıklatın.
4. **Bellek dökümünü** > Yükle**tam dökümünü**Seç ' i seçin.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Web uygulamamın işlem düzeyi bilgisini Nasıl yaparım? görüntüle?

Web uygulamanız için işlem düzeyi bilgileri görüntülemek üzere iki seçeneğiniz vardır:

*   Azure portalında:
    1. Web uygulaması için **Işlem Gezginini** açın.
    2. Ayrıntıları görmek için **W3wp. exe** işlemini seçin.
*   Kudu konsolunda:
    1. [Kudu Web sitenizde](https://*yourwebsitename*.scm.azurewebsites.net)oturum açın.
    2. **Işlem Gezgini** menüsünü seçin.
    3. **W3wp. exe** Işlemi için **Özellikler**' i seçin.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Uygulamama gözatarken "hata 403-Bu Web uygulaması durduruldu" görüyorum. Nasıl yaparım? bunu çözmek mi istiyorsunuz?

Üç koşul bu hataya neden olabilir:

* Web uygulaması bir faturalandırma sınırına ulaştı ve siteniz devre dışı bırakıldı.
* Web uygulaması portalda durduruldu.
* Web uygulaması, ücretsiz veya paylaşılan bir ölçek hizmeti planına uygulanabilecek bir kaynak kotası sınırına ulaştı.

Hataya neyin neden olduğunu görmek ve sorunu çözmek için Web Apps içindeki [adımları izleyin: "Hata 403 – Bu Web uygulaması durduruldu"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Çeşitli App Service planlarına yönelik kotalar ve sınırlar hakkında nereden daha fazla bilgi edinebilirim?

Kotalar ve sınırlamalar hakkında bilgi için bkz. [App Service sınırları](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Nasıl yaparım?, boşta kalma zamanından sonra ilk isteğin yanıt süresini düşürsin mi?

Varsayılan olarak, Web Apps, belirlenen süre boyunca boşta kalırsa kaldırılır. Bu şekilde, sistem kaynakları tasarrufu sağlayabilir. Downsıde, Web uygulamasının bellekten kaldırıldıktan sonraki ilk istek için yanıtın daha uzun olduğundan, Web uygulamasının yanıt vermeye çalışmasına izin verir. Temel ve standart hizmet planlarında, uygulamanın her zaman yüklenmesini sağlamak için **her zaman açık** ayarını açabilirsiniz. Bu, uygulama boşta kaldıktan sonra yükleme sürelerinin uzamayı ortadan kaldırır. **Her zaman açık** ayarını değiştirmek için:

1. Azure portal web uygulamanıza gidin.
2. **Uygulama ayarları**' nı seçin.
3. **Her zaman açık**için **Açık**seçeneğini belirleyin.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Başarısız istek izlemeyi açmak Nasıl yaparım? mı?

Başarısız istek izlemeyi açmak için:

1. Azure portal web uygulamanıza gidin.
3. **Tüm ayarlar** > **tanılama günlükleri**' ni seçin.
4. **Başarısız Istek izleme**için **Açık**' ı seçin.
5. **Kaydet**’i seçin.
6. Web uygulaması dikey penceresinde **Araçlar**' ı seçin.
7. **Visual Studio Online**' ı seçin.
8. Ayar **Açık**değilse **Açık**' ı seçin.
9. **Git**' i seçin.
10. **Web. config**' i seçin.
11. System. webServer içinde, bu yapılandırmayı ekleyin (belirli bir URL 'YI yakalamak için):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Yavaş performans sorunlarını gidermek için, bu yapılandırmayı ekleyin (yakalama isteği 30 saniyeden uzun sürerse):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Başarısız istek izlemelerini indirmek için, [portalda](https://portal.azure.com)Web sitenize gidin.
15. **Araçlar** > kuduGit > ' i seçin.
18. Menüsünde, **Hata Ayıkla konsol** > **cmd**' yi seçin.
19. **LogFiles** klasörünü seçin ve ardından **W3SVC**ile başlayan bir adı olan klasörü seçin.
20. XML dosyasını görmek için kalem simgesini seçin.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>"Bellek yüzdesi ' sınırı nedeniyle çalışan Işlemi geri dönüşümü istedi." iletisini görüyorum. " Nasıl yaparım? bu sorunu ele veriyor musunuz?

32 bitlik bir işlem için kullanılabilir maksimum bellek miktarı (64 bit işletim sisteminde bile) 2 GB 'dir. Varsayılan olarak, çalışan işlemi App Service (eski web uygulamalarıyla uyumluluk için) olarak 32 bit olarak ayarlanır.

Web çalışanı rolünüzde bulunan ek belleğin avantajlarından yararlanabilmek için 64 bitlik işlemlere geçmeyi düşünün. Bu, bir Web uygulamasının yeniden başlatılmasını tetikler, bu nedenle zamanlamaya göre zamanlayın.

Ayrıca, 64 bitlik bir ortamın temel veya standart bir hizmet planı gerektirdiğini de unutmayın. Ücretsiz ve paylaşılan planlar her zaman 32 bitlik bir ortamda çalışır.

Daha fazla bilgi için bkz. [App Service Web uygulamalarını yapılandırma](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Neden 230 saniye sonra isteğim zaman aşımına uğrar?

Azure Load Balancer, dört dakikalık varsayılan bir boşta kalma zaman aşımı ayarına sahiptir. Bu genellikle bir Web isteği için makul bir yanıt süresi limiti olur. Web uygulamanız arka planda işleme gerektiriyorsa, Azure WebJobs kullanmanız önerilir. Azure Web uygulaması, Web Işleri çağırabilir ve arka plan işleme tamamlandığında bildirim alabilir. Kuyruklar ve Tetikleyiciler dahil olmak üzere WebJobs kullanımı için birden çok yöntemden birini seçebilirsiniz.

WebJobs, arka plan işlemesi için tasarlanmıştır. WebJob 'ta istediğiniz kadar çok arka plan işlemi yapabilirsiniz. WebJobs hakkında daha fazla bilgi için bkz. [WebJobs ile arka plan görevleri çalıştırma](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>App Service barındırılan ASP.NET Core uygulamalar bazen yanıt vermeyi durdurabilir. Nasıl yaparım? bu sorun düzeltilsin mi?

Önceki bir [Kestrel sürümü](https://github.com/aspnet/KestrelHttpServer/issues/1182) ile ilgili bilinen bir sorun, App Service içinde barındırılan ASP.NET Core 1,0 uygulamasının zaman zaman yanıt vermeyi durdurmasına neden olabilir. Şu iletiyi de görebilirsiniz: "Belirtilen CGI uygulaması bir hatayla karşılaştı ve sunucu işlemi sonlandırdı."

Bu sorun Kestrel Version 1.0.2 içinde düzeltilmiştir. Bu sürüm ASP.NET Core 1.0.3 güncelleştirmesine dahildir. Bu sorunu çözmek için, Kestrel 1.0.2 kullanmak üzere uygulama bağımlılıklarınızı güncelleştirdiğinizden emin olun. Alternatif olarak, [App Service Web Apps 'te 1,0 yavaş performans sorunları ASP.NET Core](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites)blog postasında açıklanan iki geçici çözüm de kullanabilirsiniz.


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Günlük dosyalarımı Web uygulamamın dosya yapısında bulamıyorum. Bunları nasıl bulabilirim?

App Service yerel önbellek özelliğini kullanıyorsanız, App Service örneğiniz için LogFiles ve veri klasörlerinin klasör yapısı etkilenir. Yerel önbellek kullanıldığında, alt klasörler depolama günlük dosyaları ve veri klasörlerinde oluşturulur. Alt klasörler "benzersiz tanımlayıcı" ve zaman damgasını adlandırma düzenlerini kullanır. Her alt klasör, Web uygulamasının çalıştığı veya çalıştırıldığı bir VM örneğine karşılık gelir.

Yerel önbellek kullanıp kullanmayacağınızı anlamak için App Service **uygulama ayarları** sekmesini kontrol edin. Yerel önbellek kullanılıyorsa, uygulama ayarı `WEBSITE_LOCAL_CACHE_OPTION` olarak `Always`ayarlanır.

Yerel önbellek kullanmıyorsanız ve bu sorunla karşılaşıyorsanız, bir destek isteği gönderin.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>"Bir yuvaya erişim izinleri tarafından yasaklanmış bir şekilde erişme girişiminde bulunuldu" iletisini görüyorum. Nasıl yaparım? bunu çözmek mi istiyorsunuz?

Bu hata genellikle VM örneğindeki giden TCP bağlantıları tükendiğinde oluşur. App Service, her bir sanal makine örneği için yapılabilecek en fazla giden bağlantı sayısı için sınırlar zorlanır. Daha fazla bilgi için bkz. [VM 'Ler arası sayısal sınırlar](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Bu hata, uygulamanızdan yerel bir adrese erişmeyi denerseniz da oluşabilir. Daha fazla bilgi için bkz. [Yerel adres istekleri](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Web uygulamanızdaki giden bağlantılar hakkında daha fazla bilgi için bkz. [Azure Web siteleri 'ne giden bağlantılar](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/)hakkında blog gönderisi.

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Nasıl yaparım? App Service Web uygulamamda uzaktan hata ayıklama yapmak için Visual Studio 'Yu kullanın mi?

Visual Studio 'Yu kullanarak Web uygulamanızda hata ayıklamanın nasıl yapılacağını gösteren ayrıntılı bir anlatım için, bkz. [App Service Web uygulamanızda uzaktan hata ayıklama](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).

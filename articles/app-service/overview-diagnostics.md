---
title: Azure App Service tanılamada genel bakış | Microsoft Docs
description: App Service Tanılama ile uygulamanızdaki sorunları nasıl giderebileceğinizi öğrenin.
keywords: App Service, Azure App Service, tanılama, destek, Web uygulaması, sorun giderme, kendi kendine yardım
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: 2919c7a2fa3a8912de0272af5131583988184704
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755524"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service tanılamada genel bakış

Bir Web uygulaması çalıştırırken, 500 hatalardan, kullanıcılarınızın sitenizin bittiğini söyleyen bir sorun oluşabilecek sorunlar için hazırlıklı olmak istersiniz. App Service tanılamayı yapılandırma gerekmeden uygulamanızı gidermenize yardımcı olacak akıllı ve etkileşimli bir deneyimdir. Uygulamanız ile ilgili sorunlar yaşıyorsanız, App Service tanılama, sorunu daha kolay ve hızlı bir şekilde gidermeye ve çözmeye yönelik doğru bilgilere kılavuzluk etmek için neyin yanlış olduğunu gösterir.

Son 24 saat içinde uygulamanızla ilgili sorun yaşadığınızda bu deneyim en çok yararlı olsa da, tüm tanılama grafikleri her zaman analiz etmeniz için kullanılabilir.

App Service tanılama yalnızca Windows üzerinde uygulamanız değil, [Linux/kapsayıcılar](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [App Service ortamı](https://docs.microsoft.com/azure/app-service/environment/intro)ve [Azure işlevleri](https://docs.microsoft.com/azure/azure-functions/functions-overview)üzerinde de uygulamalar için çalışır.

## <a name="open-app-service-diagnostics"></a>App Service tanılamayı aç

App Service tanılama 'ya erişmek için, App Service Web uygulamanıza veya [Azure portal](https://portal.azure.com)App Service ortamı gidin. Sol gezinti bölmesinde, **sorunları Tanıla ve çöz**' e tıklayın.

Azure Işlevleri için, işlev uygulamanıza gidin ve üst gezinti bölmesinde, **platform özellikleri**' ne tıklayın ve **kaynak yönetimi** bölümündeki **sorunları Tanıla ve çöz** ' ü seçin.

App Service tanılama giriş sayfasında, her bir giriş sayfası kutucuğunda anahtar kelimeleri kullanarak uygulamanızdaki sorunu en iyi şekilde açıklayan kategoriyi seçebilirsiniz. Ayrıca, Bu sayfa Windows uygulamaları için **Tanılama araçları** bulabileceğiniz yerdir. Bkz. [Tanılama araçları (yalnızca Windows uygulaması için)](#diagnostic-tools-only-for-windows-app).

![Değiştirir](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Etkileşimli arabirim

Uygulamanızın sorunuyla en iyi şekilde hizalanan bir giriş sayfası kategorisi seçtiğinizde App Service Tanılama ' etkileşimli arabirimi, Genie, uygulamanızda sorunu tanılamanıza ve çözmenize yardımcı olabilir. İlgilendiğiniz sorun kategorisinin tam Tanılama raporunu görüntülemek için Genie tarafından sunulan kutucuk kısayollarını kullanabilirsiniz. Kutucuk kısayolları, tanılama ölçümlerinize erişmenin doğrudan bir yolunu sağlar.

![Kutucuk kısayolları](./media/app-service-diagnostics/tile-shortcuts-2.png)

Bu kutucuklara tıkladıktan sonra, kutucukta açıklanan sorunla ilgili konuların bir listesini görebilirsiniz. Bu konular, tüm rapordaki önemli bilgi parçacıklarını sağlar. Sorunları daha fazla araştırmak için bu konulardan birine tıklayabilirsiniz. Ayrıca, tek bir sayfadaki tüm konuları araştırmak için **tam raporu görüntüle** ' ye tıklayabilirsiniz.

![Konular](./media/app-service-diagnostics/application-logs-insights-3.png)

![Tam raporu görüntüle](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Tanılama raporu

Konuyu bir konuya tıklayarak daha ayrıntılı bir şekilde araştırmaya çalıştıktan sonra, grafiklerle ve markmalar ile sık olarak açıklanan konuyla ilgili daha fazla ayrıntı görüntüleyebilirsiniz. Tanılama raporu, uygulamanızda sorunu işaret etmek için güçlü bir araç olabilir.

![Tanılama raporu](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Sistem durumu kurulumu

Uygulamanızda neyin yanlış olduğunu bilmiyorsanız veya sorunlarınızı gidermeye nereden başlayabileceğinizi bilmiyorsanız, sistem durumunun başlatılması iyi bir yerdir. Sistem durumu kontrolü, uygulamalarınızın ne olduğunu ve neyin yanlış olduğunu gösteren hızlı ve etkileşimli bir genel bakış sunan ve sorunu araştırmak için nereye bakabileceğiniz konusunda bilgi almak üzere uygulamalarınızı analiz ediyor. Akıllı ve etkileşimli arabirimi, sorun giderme işlemiyle ilgili rehberlik sağlar. Sistem durumu, Windows uygulamaları için Genie deneyimiyle ve Linux uygulamaları için Web uygulaması aşağı tanılama raporuna tümleştirilir.

### <a name="health-checkup-graphs"></a>Sistem durumu denetim grafikleri

Sistem durumu denetiminde dört farklı grafik vardır.

- **İstekler ve hatalar:** HTTP sunucu hatalarıyla birlikte, son 24 saat içinde yapılan isteklerin sayısını gösteren bir grafik.
- **uygulama performansı:** Çeşitli yüzdebirlik grupları için son 24 saat içindeki yanıt süresini gösteren bir grafik.
- **CPU kullanımı:** Son 24 saat içinde örnek başına yüzde CPU kullanımının genel olarak gösterildiği bir grafik.  
- **bellek kullanımı:** Son 24 saat içinde örnek başına yüzde fiziksel bellek kullanımının genel olarak gösterildiği bir grafik.

![Sistem durumu kurulumu](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Uygulama kodu sorunlarını araştır (yalnızca Windows uygulaması için)

Birçok uygulama sorunu uygulama kodunuzda sorunlarla ilişkili olduğundan, App Service tanılama, seçili kesinti ile ilişkilendirmek üzere özel durumları ve bağımlılık sorunlarını vurgulamak için [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) ile tümleşir. Application Insights ayrı olarak etkinleştirilmelidir.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Application Insights özel durumları ve bağımlılıkları görüntülemek için **Web uygulaması** ' nı veya **Web uygulaması yavaş** kutucuk kısayollarını seçin.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Sorun giderme adımları (yalnızca Windows uygulaması için)

Son 24 saat içinde belirli bir sorun kategorisiyle bir sorun algılanırsa, tam Tanılama raporunu görüntüleyebilirsiniz ve App Service tanılama, daha fazla işlem yapmak için daha fazla sorun giderme önerisi ve sonraki adımları görüntülemenizi isteyebilir.

![Application Insights ve sorun giderme ve sonraki adımlar](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Tanılama araçları (yalnızca Windows uygulaması için)

Tanılama araçları, uygulama kodu sorunlarını, yavaşlamaları, bağlantı dizelerini ve daha fazlasını araştırmanıza yardımcı olan daha gelişmiş tanılama araçları içerir. ve CPU kullanımı, istekler ve bellekle ilgili sorunları azaltmanıza yardımcı olan proaktif araçlar.

### <a name="proactive-cpu-monitoring"></a>Proaktif CPU izleme

Proaktif CPU izleme, uygulamanızın uygulamanız veya alt işleminiz yüksek CPU kaynakları tükettiği zaman bir işlem yapmak için kolay ve proaktif bir yol sağlar. Beklenmeyen sorunun gerçek nedeni bulunana kadar yüksek CPU bir sorunu geçici olarak azaltmak için kendi CPU eşik kurallarınızı ayarlayabilirsiniz. Daha fazla bilgi için bkz. [CPU sorunlarınızı gerçekleşmeden önce azaltma](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Proaktif CPU izleme](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Otomatik Düzeltme ve proaktif otomatik düzeltme

Otomatik düzeltme, uygulamanız beklenmeyen davranışlara sahip olduğunda gerçekleştirebileceğiniz bir azaltma eylemi eylemdir. Risk azaltma eylemlerini tetiklemek için istek sayısı, yavaş istek, bellek sınırı ve HTTP durum koduna göre kendi kurallarınızı ayarlayabilirsiniz. Asıl nedeni bulana kadar beklenmeyen bir davranışı geçici olarak hafifletmek için aracını kullanın. Daha fazla bilgi için bkz. [App Service tanılamasında yeni otomatik düzeltme deneyimi duyurusu](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Otomatik onarım](./media/app-service-diagnostics/auto-healing-10.png)

Proaktif CPU izleme gibi, öngörülü otomatik düzeltme, uygulamanızın beklenmedik bir şekilde davranışını azaltmaya yönelik bir açılan anahtar çözümüdür. Proaktif otomatik düzeltme, App Service uygulamanızın kurtarılamaz bir durumda olduğunu belirlediğinde uygulamanızı yeniden başlatır. Daha fazla bilgi için bkz. [proaktif Otomatik Heal tanıtımı](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Gezgin ve değişiklik Analizi (yalnızca Windows uygulaması için)

Sürekli tümleştirme ile büyük bir takımda ve uygulamanızın birçok bağımlılığı olduğu durumlarda, sağlıksız davranışa neden olan belirli bir değişikliği saptamak zor olabilir. Gezgin, uygulamanızın bir bağımlılık haritasını ve aynı abonelikteki tüm kaynakları otomatik olarak silerek uygulamanızın topolojisi üzerinde görünürlük sağlanmasına yardımcı olur. Gezgin, uygulamanız ve bağımlılıkları tarafından yapılan değişikliklerin birleştirilmiş bir listesini görüntülemenize olanak sağlar ve bir değişikliği kötü durumda davranışa neden olan bir değişikliğe karşı daraltın. Giriş sayfası kutucuk **Gezgini** aracılığıyla erişilebilir ve ilk kez kullanılmadan önce etkinleştirilmesi gerekir. Daha fazla bilgi için bkz. [Gezgin ile uygulamanızın bağımlılıklarıyla ilgili görünürlük edinme](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Gezgin varsayılan sayfası](./media/app-service-diagnostics/navigator-default-page-11.png)

![Fark görünümü](./media/app-service-diagnostics/diff-view-12.png)

Uygulama değişikliklerine yönelik değişiklik analizine, kutucuk kısayolları, **uygulama değişiklikleri** ve **uygulama kilitlenmeleri** aracılığıyla erişilebilir **ve** bu sayede diğer ölçümler ile aynı anda kullanabilirsiniz. Özelliği kullanmadan önce, önce etkinleştirmelisiniz. Daha fazla bilgi için bkz. [App Service tanılama 'da yeni değişiklik Analizi deneyimi duyurusu](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Başlığa "[diag]" ekleyerek sorularınızı veya görüşlerinizi [UserVoice](https://feedback.azure.com/forums/169385-web-apps) 'a gönderin.

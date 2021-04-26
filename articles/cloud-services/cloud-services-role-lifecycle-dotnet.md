---
title: Bulut hizmeti (klasik) yaşam döngüsü olaylarını işleme | Microsoft Docs
description: .NET 'teki bir bulut hizmeti rolünün yaşam döngüsü yöntemlerini, yaşam döngüsü olaylarına yanıt vermek için yöntemler sağlayan RoleEntryPoint dahil olmak üzere nasıl kullanacağınızı öğrenin.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b5aa4bd061647f63ebcc70109f0ba21b39e814cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741341"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>.NET ' te bir Web veya çalışan rolünün yaşam döngüsünü özelleştirme

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Bir çalışan rolü oluşturduğunuzda, yaşam döngüsü olaylarına yanıt vermenize izin veren Yöntemler sağlayan [Roleentrypoint](/previous-versions/azure/reference/ee758619(v=azure.100)) sınıfını genişletebilirsiniz. Web rolleri için bu sınıf isteğe bağlıdır, bu nedenle yaşam döngüsü olaylarına yanıt vermek için kullanılmalıdır.

## <a name="extend-the-roleentrypoint-class"></a>RoleEntryPoint sınıfını genişletme
[Roleentrypoint](/previous-versions/azure/reference/ee758619(v=azure.100)) sınıfı, bir Web veya çalışan rolünü **başlatan**, **çalıştırdığında** veya **durdurduğu** zaman Azure tarafından çağrılan yöntemleri içerir. Rol başlatma, rol kapatması sıraları veya rolün yürütme iş parçacığını yönetmek için isteğe bağlı olarak bu yöntemleri geçersiz kılabilirsiniz. 

**Roleentrypoint**'yi genişletirken, yöntemlerin aşağıdaki davranışlarından haberdar olmanız gerekir:

* [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) yöntemi bir Boole değeri döndürür, bu nedenle bu yöntemden **false** döndürmek mümkündür.
  
   Kodunuz **yanlış** döndürürse, yapmış olduğunuz herhangi bir kapalı sırası çalıştırmadan rol işlemi aniden sonlandırılır. Genel olarak, **OnStart** yönteminden **false** döndürmekten kaçınmalısınız.
* **Roleentrypoint** yönteminin aşırı yüklemesi içindeki yakalanamayan özel durum, işlenmemiş bir özel durum olarak değerlendirilir.
  
   Yaşam döngüsü yöntemlerinden biri içinde bir özel durum oluşursa, Azure [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) olayını yükseltir ve sonra işlem sonlandırılır. Rolünüz çevrimdışına alındıktan sonra Azure tarafından yeniden başlatılır. İşlenmeyen bir özel durum oluştuğunda, [durdurma](/previous-versions/azure/reference/ee758136(v=azure.100)) olayı oluşturulmaz ve **OnStop** yöntemi çağrılmaz.

Rolünüzün başlatılması veya başlatma, meşgul ve durdurma durumları arasında geri dönüştürülmesi durumunda, kodunuz rol her yeniden başlatıldığında bir yaşam döngüsü olaylarında işlenmeyen bir özel durum verebilir. Bu durumda, özel durumun nedenini öğrenmek ve uygun şekilde işlemek için [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) olayını kullanın. Rolünüz, rolün yeniden başlatılmasına neden olan [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) yönteminden da döndürülemeyebilir. Dağıtım durumları hakkında daha fazla bilgi için bkz. [rollerin geri dönüştürülmesine neden olan yaygın sorunlar](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Uygulamanızı geliştirmek için **Microsoft Visual Studio Için Azure araçlarını** kullanıyorsanız, rol proje şablonları, *WebRole. cs* ve *Workerrole. cs* dosyalarındaki **roleentrypoint** sınıfını sizin için otomatik olarak genişletir.
> 
> 

## <a name="onstart-method"></a>OnStart yöntemi
**OnStart** yöntemi, rol örneğiniz Azure tarafından çevrimiçi yapıldığında çağrılır. OnStart kodu yürütülürken, rol örneği **meşgul** olarak işaretlenir ve yük dengeleyici tarafından buna hiçbir dış trafik yönlendirilmeyecektir. Olay işleyicilerini uygulama ve [Azure tanılama](cloud-services-how-to-monitor.md)başlatma gibi başlatma işlerini gerçekleştirmek için bu yöntemi geçersiz kılabilirsiniz.

**OnStart** **doğru** döndürürse, örnek başarıyla başlatılır ve Azure **roleentrypoint. Run** yöntemini çağırır. **OnStart** **yanlış** döndürürse, rol, planlı herhangi bir kapalı dizileri yürütmeden hemen sona erer.

Aşağıdaki kod örneği, **OnStart** yönteminin nasıl geçersiz kılınacağını göstermektedir. Bu yöntem, rol örneği başlatıldığında bir tanılama izleyicisini yapılandırır ve başlatır ve günlüğe kaydetme verilerinin bir depolama hesabına aktarımını ayarlar:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop yöntemi
**OnStop** yöntemi, bir rol örneği Azure tarafından çevrimdışına alındıktan sonra ve işlem çıkmadan önce çağrılır. Rol örneğiniz için gereken kodu, düzgün şekilde kapatılacak şekilde çağırmak için bu yöntemi geçersiz kılabilirsiniz.

> [!IMPORTANT]
> **OnStop** yönteminde çalışan kodun, Kullanıcı tarafından başlatılan bir kapatmasından farklı nedenlerle çağrıldığında tamamlaması sınırlı bir zaman vardır. Bu süre dolduktan sonra, işlem sonlandırılır, bu nedenle **OnStop** yöntemindeki kodun, tamamlanana kadar hızlı veya tolerans ile çalışmasını sağlamak zorundasınız. **OnStop** yöntemi, **durdurma** olayı oluşturulduktan sonra çağrılır.
> 
> 

## <a name="run-method"></a>Run yöntemi
Rol örneğiniz için uzun süre çalışan bir iş parçacığı uygulamak üzere **Run** metodunu geçersiz kılabilirsiniz.

**Run** yönteminin geçersiz kılınması gerekli değildir; Varsayılan uygulama, sonsuza kadar uykudaki bir iş parçacığını başlatır. **Run** yöntemini geçersiz kılarsınız, kodunuz süresiz olarak engellenmelidir. **Run** yöntemi döndürürse, rol otomatik olarak düzgün şekilde geri dönüştürülür; diğer bir deyişle, Azure **durdurma** olayını yükseltir ve **OnStop** metodunu çağırarak, bu sayede, rol çevrimdışına alınmadan önce kapalı olması gerekir.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Web rolü için ASP.NET yaşam döngüsü yöntemlerini uygulama
Bir Web rolü için başlatma ve kapalı dizileri yönetmek üzere **Roleentrypoint** sınıfı tarafından sağlananlara ek olarak ASP.net yaşam döngüsü yöntemlerini kullanabilirsiniz. Mevcut bir ASP.NET uygulamasını Azure 'a taşırken bu, uyumluluk amacıyla yararlı olabilir. ASP.NET yaşam döngüsü yöntemleri **Roleentrypoint** yöntemlerinin içinden çağırılır. **Uygulama \_ başlatma** yöntemi **roleentrypoint. OnStart** yöntemi bittikten sonra çağrılır. **Uygulama \_ End** yöntemi **roleentrypoint. OnStop** yöntemi çağrılmadan önce çağrılır.

## <a name="next-steps"></a>Sonraki adımlar
[Bulut hizmeti paketi oluşturmayı](cloud-services-model-and-package.md)öğrenin.





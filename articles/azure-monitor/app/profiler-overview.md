---
title: Azure 'da üretim uygulamalarını Application Insights Profiler ile profili | Microsoft Docs
description: Web sunucusu kodunuzda düşük boyutlu bir profil Oluşturucu ile etkin yolu belirler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: fc152aab6d0e62ac5656b50834ce17278bb6676e
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820511"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Application Insights ile Azure 'da üretim uygulamaları profilini yapın
## <a name="enable-application-insights-profiler-for-your-application"></a>Uygulamanız için Application Insights Profiler etkinleştirme

Azure Application Insights Profiler, Azure 'da üretimde çalışan uygulamalar için performans izlemeleri sağlar. Profil Oluşturucu, kullanıcılarınızı olumsuz şekilde etkilemeden verileri otomatik olarak ölçeklendirerek yakalar. Profiler, belirli bir web isteğini işlerken en uzun süreyi alan "sık erişimli" kod yolunu belirlemenize yardımcı olur. 

Profil Oluşturucu, aşağıdaki Azure hizmetlerinde dağıtılan .NET uygulamalarıyla birlikte çalışmaktadır. Her hizmet türü için profil oluşturucuyu etkinleştirmeye yönelik belirli yönergeler aşağıdaki bağlantılardadır.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure sanal makineleri ve sanal makine ölçek kümeleri](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**Önizleme** ASP.NET Core Azure Linux Web Apps](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Profil oluşturucuyu etkinleştirdiyseniz, ancak izlemeleri görmüyorsanız, [sorun giderme kılavuzumuzu](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)kontrol edin.

## <a name="view-profiler-data"></a>Profil oluşturucu verilerini görüntüle

İzlemeleri karşıya yüklemek için profil oluşturucunun, uygulamanızın istekleri etkin bir şekilde işlemesi gerekir. Deneme yapıyorsanız, [Application Insights performans testi](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)kullanarak Web uygulamanıza istek oluşturabilirsiniz. Profil oluşturucuyu yeni etkinleştirdiyseniz, kısa bir yük testi çalıştırabilirsiniz. Yük testi çalışırken, profil [ **Oluşturucu ayarları** bölmesinde](profiler-settings.md) **Şimdi profil** düğmesini seçin. Profil Oluşturucu çalışırken, bir saatte bir kez ve iki dakikalık bir süre boyunca rastgele bir profil oluşturulur. Uygulamanız sürekli bir istek akışını işliyorsa, profil oluşturucu karşıya yüklemeleri her saat için izler.

Uygulamanız bazı trafiği aldıktan sonra ve profil oluşturucunun izlemeleri karşıya yükleme zamanı varsa, görüntülenecek izlemelere sahip olmanız gerekir. Bu işlem, 5 ila 10 dakika sürebilir. İzlemeleri görüntülemek için **performans** bölmesinde **eylemleri al**' ı seçin ve ardından **Profil Oluşturucu izlemeleri** düğmesini seçin.

![Application Insights performans bölmesi önizleme profil oluşturucu izlemeleri][performance-blade]

İsteği yürütmek için harcanan sürenin kod düzeyinde dökümünü görüntülemek için bir örnek seçin.

![Application Insights izleme Gezgini][trace-explorer]

İzleme Gezgini aşağıdaki bilgileri görüntüler:

* **Etkin yolu göster**: en büyük yaprak düğümü veya en az bir kapanış açar. Çoğu durumda bu düğüm performans sorununa yaklaştı.
* **Etiket**: işlevin veya etkinliğin adı. Ağaç, SQL ve HTTP olayları gibi oluşan kod ve olayların bir karışımını görüntüler. En üstteki olay genel istek süresini temsil eder.
* **Geçen**: işlemin başlangıcı ile işlemin sonu arasındaki zaman aralığı.
* **Ne zaman**: işlevin veya etkinliğin diğer işlevlerle ilişkili olarak çalıştığı zaman.

## <a name="how-to-read-performance-data"></a>Performans verilerini okuma

Microsoft hizmet profili Oluşturucu, uygulamanızın performansını analiz etmek için bir örnekleme yöntemleri ve izleme birleşimi kullanır. Ayrıntılı koleksiyon devam ederken, hizmet profili Oluşturucu her bir makine CPU 'nun her milisaniyelik yönerge işaretçisini örnekler. Her örnek, şu anda yürütülmekte olan iş parçacığının tüm çağrı yığınını yakalar. Bu iş parçacığının ne yaptığını, hem yüksek düzeyde hem de düşük bir soyutlama düzeyinde ayrıntılı bilgiler verir. Hizmet profili Oluşturucu, bağlam değiştirme olayları, görev paralel kitaplığı (TPL) olayları ve iş parçacığı havuzu olayları da dahil olmak üzere etkinlik bağıntısını ve kullanılabilirliğini izlemek için diğer olayları da toplar.

Zaman çizelgesi görünümünde görüntülenen çağrı yığını örnekleme ve izleme sonucudur. Her örnek iş parçacığının tüm çağrı yığınını yakaladığından, Microsoft .NET Framework 'ten ve başvurduğunuz diğer çerçevelerden kod içerir.

### <a id="jitnewobj"></a>Nesne ayırma (clr! JıT\_yeni veya clr! JıT\_Newarr1)

**clr! JıT\_yeni** ve **clr! JıT\_Newarr1** , yönetilen bir yığından bellek ayıran .NET Framework yardımcı işlevlerdir. **clr! JıT\_yeni** bir nesne ayrıldığında çağrılır. **clr!** Bir nesne dizisi AYRıLDıĞıNDA jıt\_Newarr1 çağrılır. Bu iki işlev genellikle hızlıdır ve görece az miktarda zaman alır. Eğer **clr! JıT\_yeni** veya **clr! JıT\_Newarr1** zaman çizelgenizde çok zaman alır, kod birçok nesne ayırarak ve önemli miktarda bellek tüketiyor olabilir.

### <a id="theprestub"></a>Kod yükleniyor (clr! ThePreStub)

**clr! ThePreStub** , kodu ilk kez yürütmek üzere hazırlayan .NET Framework bir yardımcı işlevdir. Bu yürütme genellikle tam zamanında (JıT) derlemeyi içerir ancak bunlarla sınırlı değildir. Her C# Yöntem için **clr!** Bir işlem sırasında, ön saplama en fazla bir kez çağrılmalıdır.

Eğer **clr! Ön saplama** bir istek için uzun bir süre sürer, istek bu yöntemi yürütmek için birinci bir yöntemdir. .NET Framework çalışma zamanının ilk yöntemi yüklemesi için zaman önemlidir. Kullanıcılarınızın erişemez veya yerel görüntü Oluşturucu (Ngen. exe) derlemelerinize çalıştırmayı düşünmeden önce kodun bu kısmını yürüten bir ısınma işlemi kullanmayı düşünebilirsiniz.

### <a id="lockcontention"></a>Kilit çakışması (clr! Monçekişme veya clr\_Jlıutil! Jutil\_MonEnterWorker)

**clr! Monçekişme veya clr\_Jlıutil** **! Jutil\_MonEnterWorker** , geçerli iş parçacığının bir kilidin serbest bırakılacağını beklediğini gösterir. Bu metin genellikle bir C# **kilit** ifadesini yürüttüğünüzde, **Monitor. ENTER** metodunu çağırdığınızda veya **MethodImplOptions. eşitlenmiş** özniteliğiyle bir yöntemi çağırdığınızda görüntülenir. Kilit çakışması genellikle _iş parçacığı bir_ kilit aldığında ve _B_ _iş parçacığı BT tarafından serbest bırakmadan_ önce aynı kilidi almaya çalıştığında oluşur.

### <a id="ngencold"></a>Kod yükleniyor ([soğuk])

Yöntem adı **[soğuk]** içeriyorsa, **mscorlib.ni! [ SOĞUK] System. Reflection. CustomAttribute. IsDefined**.NET Framework çalışma zamanı, [Profil temelli iyileştirme](/cpp/build/profile-guided-optimizations)tarafından en iyi duruma getirilmeyen ilk kez kodu yürütüyor. Her yöntem için, işlem sırasında en fazla bir kez görüntülenmelidir.

Kod yükleme bir istek için önemli miktarda zaman alıyorsa istek, metodun en iyi duruma getirilmeyen kısmını yürütmek için ilk bir seçenektir. Kullanıcılarınızın ona erişmeden önce kodun bu bölümünü yürüten bir ısınma işlemi kullanmayı düşünün.

### <a id="httpclientsend"></a>HTTP isteği gönder

**HttpClient. Send** gibi yöntemler, kodun bir http isteğinin tamamlanmasını beklediğini gösterir.

### <a id="sqlcommand"></a>Veritabanı işlemi

**SqlCommand. Execute** gibi yöntemler, kodun bir veritabanı işleminin bitmesini beklediğini gösterir.

### <a id="await"></a>Bekleniyor (AWAIT\_süre)

**AWAIT\_zamanı** , kodun başka bir görevin bitmesini beklediğini gösterir. Bu gecikme genellikle C# **AWAIT** ifadesiyle oluşur. Kod bir C# **AWAIT**yaparken, iş parçacığı geri almıyor ve denetim iş parçacığı havuzuna geri döndürüyor ve **AWAIT** 'in bitmesi beklenirken engellenen iş parçacığı yok. Ancak mantıksal olarak, **AWAIT** olan iş parçacığı "engelleniyor" olur ve işlemin tamamlanmasını bekler. **AWAIT\_TIME** ifadesinde, görevin bitmesi için bekleyen engellenme süresi gösterilir.

### <a id="block"></a>Engellenme süresi

**BLOCKED_TIME** , kodun başka bir kaynağın kullanılabilir olmasını beklediğini gösterir. Örneğin, bir eşitleme nesnesi için bir iş parçacığının kullanılabilir olması veya bir isteğin bitmesi bekleniyor olabilir.

### <a name="unmanaged-async"></a>Yönetilmeyen zaman uyumsuz

.NET Framework ETW olaylarını yayar ve zaman uyumsuz çağrıların iş parçacıkları arasında izlenmesini sağlamak için iş parçacıkları arasında etkinlik kimliklerini geçirir. Yönetilmeyen kod (yerel kod) ve zaman uyumsuz kodun bazı eski stillerinde bu olaylar ve etkinlik kimlikleri eksiktir, bu nedenle profil oluşturucu iş parçacığı hangi iş parçacığını ve hangi işlevlerin çalıştığını söylemez. Bu, çağrı yığınında ' yönetilmeyen Async ' olarak etiketlenir. ETW dosyasını indirdiğinizde, neler olduğuna ilişkin daha fazla bilgi almak için [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) 'ı kullanabilirsiniz.

### <a id="cpu"></a>CPU süresi

Bu, yönergeleri yürütürken CPU meşguldür.

### <a id="disk"></a>Disk zamanı

Uygulama, disk işlemleri gerçekleştiriyor.

### <a id="network"></a>Ağ süresi

Uygulama ağ işlemleri gerçekleştiriyor.

### <a id="when"></a>Ne zaman sütunu

**Ne zaman** sütunu, düğüm için toplanan örnekleri zaman içinde farklılık gösterir. İsteğin toplam aralığı 32 zaman demetlerine bölünür. Bu düğüm için kapsamlı örnekler bu 32 demetlerine göre biriktirilir. Her demet bir çubuk olarak temsil edilir. Çubuğun yüksekliği ölçeklendirilmiş bir değeri temsil eder. **CPU_TIME** veya **BLOCKED_TIME**olarak işaretlenmiş veya bir kaynağı (örneğin, bir CPU, disk veya iş parçacığı) tüketmeye yönelik belirgin bir ilişki olduğu durumlarda, çubuk demet sırasında kaynaklardan birinin tüketimini temsil eder. Bu ölçümler için, birden fazla kaynak tüketerek yüzde 100 ' den büyük bir değer almak mümkündür. Örneğin, bir Aralık sırasında ortalama ve iki CPU kullanırsanız, yüzde 200 ' ı alırsınız.

## <a name="limitations"></a>Sınırlamalar

Varsayılan veri saklama süresi beş gündür. Gün başına alınan en fazla veri 10 GB 'tır.

Profil Oluşturucu hizmeti kullanımı için herhangi bir ücret alınmaz. Bunu kullanabilmeniz için, Web uygulamanızın en azından Azure App Service Web Apps özelliğinin temel katmanında barındırılması gerekir.

## <a name="overhead-and-sampling-algorithm"></a>Ek yük ve örnekleme algoritması

Profiler, izlemeleri yakalamak için etkin olan uygulamayı barındıran her bir sanal makinede her bir saatte iki dakikada bir çalışır. Profil Oluşturucu çalışırken, sunucuya 5 ' ten %15 ' e kadar CPU ek yükü ekler.

## <a name="next-steps"></a>Sonraki adımlar
Azure uygulamanız için Application Insights Profiler etkinleştirin. Ayrıca bkz:
* [Uygulama Hizmetleri](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure sanal makineleri ve sanal makine ölçek kümeleri](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png

---
title: .NET uygulamaları için Azure Application Insights Snapshot Debugger | Microsoft Docs
description: Üretim .NET uygulamalarında özel durumlar oluştuğunda hata ayıklama anlık görüntüleri otomatik olarak toplanır
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/23/2019
ms.reviewer: brahmnes
ms.openlocfilehash: 33a9db5fa9f31a0c4548ecdeb6c0ca2f12ac8246
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899789"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET uygulamalarında özel durumlarla ilgili hata ayıklama anlık görüntüleri
Bir özel durum oluştuğunda, Canlı Web uygulamanızdan otomatik olarak bir hata ayıklama anlık görüntüsü toplayabilirsiniz. Anlık görüntü, kaynak kodu ve değişkenlerin durumunu özel durumun oluşturulduğu anda gösterir. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) Snapshot Debugger (Önizleme) Web uygulamanızdan özel durum telemetrisini izler. Üretim aşamasındaki sorunları tanılamak için ihtiyaç duyduğunuz bilgilere sahip olmanız için, en önemli özel durumlarınızın anlık görüntülerini toplar. [Anlık görüntü toplayıcısı NuGet paketini](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) uygulamanıza ekleyin ve isteğe bağlı olarak [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)içinde koleksiyon parametrelerini yapılandırın. Anlık görüntüler Application Insights portalındaki [özel durumlar](../../azure-monitor/app/asp-net-exceptions.md) üzerinde görünür.

Hata ayıklama anlık görüntülerini portalda görüntüleyerek çağrı yığınını görebilir ve her bir çağrı yığını çerçevesinde değişkenleri inceleyebilirsiniz. Kaynak kodla daha güçlü bir hata ayıklama deneyimi sağlamak için, Visual Studio 2019 Enterprise ile anlık görüntüler açın. Visual Studio 'da, bir özel durum beklemeden [anlık görüntüleri etkileşimli olarak almak için de anlık görüntü noktaları ayarlayabilirsiniz](https://aka.ms/snappoint) .

Hata ayıklama anlık görüntüleri 15 gün boyunca depolanır. Bu bekletme ilkesi, uygulama başına temelinde ayarlanır. Bu değeri artırmanız gerekiyorsa Azure portal bir destek talebi açarak artış isteyebilirsiniz.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Uygulamanız için Application Insights Snapshot Debugger etkinleştirin
Anlık görüntü koleksiyonu şu için kullanılabilir:
* .NET Framework 4,5 veya üstünü çalıştıran uygulamalar .NET Framework ve ASP.NET.
* .NET Core 2,0 ve ASP.NET Core 2,0 Windows üzerinde çalışan uygulamalar.

Aşağıdaki ortamlar desteklenir:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* İşletim sistemi ailesi 4 veya üstünü çalıştıran [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 veya üzeri sürümlerde çalışan [Azure Service Fabric Hizmetleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 veya üstünü çalıştıran [Azure sanal makineleri ve sanal makine ölçek kümeleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 veya üstünü çalıştıran [Şirket içi sanal veya fiziksel makineler](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> İstemci uygulamaları (örneğin, WPF, Windows Forms veya UWP) desteklenmez.

Snapshot Debugger etkinleştirdiyseniz, anlık görüntüleri görmüyorsanız, [sorun giderme kılavuzumuzu](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)kontrol edin.

## <a name="grant-permissions"></a>İzinleri verme

Anlık görüntülere erişim rol tabanlı erişim denetimi (RBAC) tarafından korunur. Bir anlık görüntüyü denetlemek için öncelikle bir abonelik sahibi tarafından gerekli role eklenmelidir.

> [!NOTE]
> Sahipler ve katkıda bulunanlar bu role otomatik olarak sahip değildir. Anlık görüntüleri görüntülemek istiyorlarsa, kendilerini role eklemesi gerekir.

Abonelik sahipleri `Application Insights Snapshot Debugger` rolünü, anlık görüntüleri denetleyecek kullanıcılara atamalıdır. Bu rol, hedef Application Insights kaynağına veya kaynak grubuna ya da aboneliğine yönelik abonelik sahiplerine bireysel kullanıcılara veya gruplara atanabilir.

1. Azure portal Application Insights kaynağına gidin.
1. **Erişim denetimi (IAM)** öğesine tıklayın.
1. **+ Rol ataması Ekle** düğmesine tıklayın.
1. **Roller** açılan listesinden **Application Insights Snapshot Debugger** seçin.
1. Arama yapmak ve Kullanıcı için bir ad girin.
1. Kullanıcıyı role eklemek için **Kaydet** düğmesine tıklayın.


> [!IMPORTANT]
> Anlık görüntüler muhtemelen değişken ve parametre değerlerinde kişisel ve diğer hassas bilgiler içerebilir.

## <a name="view-snapshots-in-the-portal"></a>Portalda anlık görüntüleri görüntüleme

Uygulamanızda bir özel durum oluştu ve bir anlık görüntü oluşturulduktan sonra, görüntülenecek anlık görüntülere sahip olmanız gerekir. Bir anlık görüntüye hazırlık ve portaldan görünebilen bir özel durumdan 5 ila 10 dakika sürebilir. Anlık görüntüleri görüntülemek için, **hata** bölmesinde, **Işlemler** sekmesini görüntülerken **Işlemler** düğmesini seçin veya **özel durumlar** sekmesini görüntülerken **özel durumlar** düğmesini seçin:

![Arızalar sayfası](./media/snapshot-debugger/failures-page.png)

Sağ bölmedeki bir işlem veya özel durum seçerek **uçtan uca Işlem ayrıntıları** bölmesini açın, sonra özel durum olayını seçin. Verilen özel durum için bir anlık görüntü varsa, sağdaki bölmede [özel durum](../../azure-monitor/app/asp-net-exceptions.md)ayrıntılarının bulunduğu bir **hata ayıklama anlık görüntüsü aç** düğmesi görünür.

![Özel durum üzerinde hata ayıklama anlık görüntüsünü aç düğmesi](./media/snapshot-debugger/e2e-transaction-page.png)

Hata ayıklama anlık görüntüsü görünümünde, bir çağrı yığını ve bir değişkenler bölmesi görürsünüz. Çağrı yığını bölmesinde çağrı yığınının çerçevelerini seçtiğinizde, bu işlev çağrısının yerel değişkenlerini ve parametrelerini değişkenler bölmesinde görüntüleyebilirsiniz.

![Portalda hata ayıklama anlık görüntüsünü görüntüleme](./media/snapshot-debugger/open-snapshot-portal.png)

Anlık görüntüler hassas bilgiler içerebilir ve varsayılan olarak görüntülenemez. Anlık görüntüleri görüntülemek için `Application Insights Snapshot Debugger` rolün size atanmış olması gerekir.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Visual Studio 2017 Enterprise veya üzeri anlık görüntüleri görüntüleme
1. Visual Studio Enterprise tarafından açılabilen bir `.diagsession` dosyası indirmek için **anlık görüntüyü indir** düğmesine tıklayın.

2. `.diagsession` dosyasını açmak için Snapshot Debugger Visual Studio bileşeninin yüklü olması gerekir. Snapshot Debugger bileşeni, Visual Studio 'daki ASP.net iş yükünün gerekli bir bileşenidir ve Visual Studio yükleyicisi 'ndeki ayrı bileşen listesinden seçilebilir. Visual Studio 2017 sürüm 15,5 ' den önceki bir Visual Studio sürümü kullanıyorsanız, uzantıyı [Visual Studio Market](https://aka.ms/snapshotdebugger)yüklemeniz gerekecektir.

3. Snapshot dosyasını açtıktan sonra, Visual Studio 'da mini döküm hata ayıklama sayfası görüntülenir. Anlık görüntüde hata ayıklamayı başlatmak için **yönetilen kodda hata ayıkla** ' ya tıklayın. Anlık görüntü, işlemin geçerli durumunda Hata ayıklayabilmeniz için özel durumun oluşturulduğu kod satırına açılır.

    ![Visual Studio 'da hata ayıklama anlık görüntüsünü görüntüleme](./media/snapshot-debugger/open-snapshot-visualstudio.png)

İndirilen anlık görüntü, Web uygulaması sunucunuzda bulunan sembol dosyalarını içerir. Anlık görüntü verilerini kaynak kodla ilişkilendirmek için bu sembol dosyaları gereklidir. App Service uygulamalar için, Web uygulamalarınızı yayımlarken sembol dağıtımını etkinleştirdiğinizden emin olun.

## <a name="how-snapshots-work"></a>Anlık görüntülerin nasıl çalıştığı

Snapshot Collector, bir [Application Insights telemetri işlemcisi](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet)olarak uygulanır. Uygulamanız çalıştığında, Snapshot Collector telemetri Işlemcisi uygulamanızın telemetri ardışık düzenine eklenir.
Uygulamanız [Trackexception](../../azure-monitor/app/asp-net-exceptions.md#exceptions)'ı her çağırdığında, Snapshot Collector oluşturulan özel durum türünden ve oluşturma yöntemiyle BIR sorun kimliği hesaplar.
Uygulamanız TrackException çağırdığında, ilgili sorun KIMLIĞI için bir sayaç artırılır. Sayaç `ThresholdForSnapshotting` değerine ulaştığında, sorun KIMLIĞI bir koleksiyon planına eklenir.

Snapshot Collector, [AppDomain. CurrentDomain. FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) olayına abone olunarak oluşturulan özel durumları da izler. Bu olay tetiklendiğinde, özel durumun sorun KIMLIĞI hesaplanır ve koleksiyon planındaki sorun kimliklerine göre karşılaştırılır.
Bir eşleşme varsa, çalışan işlemin bir anlık görüntüsü oluşturulur. Anlık görüntüye benzersiz bir tanımlayıcı atanır ve özel durum bu tanımlayıcıyla damgalı olur. FirstChanceException işleyicisi döndüğünde, oluşturulan özel durum normal olarak işlenir. Sonuç olarak, özel durum TrackException yöntemine bir kez ulaşır ve bu da anlık görüntü tanımlayıcısı ile birlikte Application Insights bildirilir.

Ana süreç çalışmaya devam eder ve kullanıcılara az kesintiye uğramış olan trafik sunar. Bu sırada anlık görüntü, Snapshot Uploader işlemine devredildir. Snapshot Uploader bir mini döküm oluşturur ve ilgili sembol (. pdb) dosyalarıyla birlikte Application Insights yükler.

> [!TIP]
> - İşlem anlık görüntüsü, çalışan işlemin askıya alınmış bir kopyadır.
> - Anlık görüntünün oluşturulması yaklaşık 10 ila 20 milisaniye sürer.
> - `ThresholdForSnapshotting` için varsayılan değer 1 ' dir. Bu aynı zamanda en küçük değerdir. Bu nedenle, uygulamanızın bir anlık görüntü oluşturulmadan aynı özel durumu **iki kez** tetiklemesi gerekir.
> - Visual Studio 'da hata ayıklarken anlık görüntüler oluşturmak istiyorsanız, `IsEnabledInDeveloperMode` true olarak ayarlayın.
> - Anlık görüntü oluşturma hızı `SnapshotsPerTenMinutesLimit` ayarıyla sınırlıdır. Varsayılan olarak, sınır her on dakikada bir anlık görüntüdür.
> - Gün başına 50 ' ten fazla anlık görüntü yüklenemez.

## <a name="limitations"></a>Sınırlamalar

Varsayılan veri saklama süresi 15 gündür. Her bir Application Insights örneği için günde en fazla 50 anlık görüntüye izin verilir.

### <a name="publish-symbols"></a>Sembolleri Yayımla
Snapshot Debugger, değişkenleri çözmek ve Visual Studio 'da bir hata ayıklama deneyimi sağlamak için üretim sunucusunda sembol dosyaları gerektirir.
Visual Studio 2017 sürüm 15,2 (veya üzeri), yayın derlemeleri için App Service yayımlandığında varsayılan olarak sembolleri yayımlar. Önceki sürümlerde, semboller yayın modunda yayınlanabilmesi için Yayımlama profili `.pubxml` dosyanıza aşağıdaki satırı eklemeniz gerekir:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Azure Işlem ve diğer türler için, sembol dosyalarının ana uygulama. dll ' de aynı klasörde olduğundan emin olun (genellikle, `wwwroot/bin`) veya geçerli yolda kullanılabilir.

> [!NOTE]
> Kullanılabilir farklı sembol seçenekleri hakkında daha fazla bilgi için [Visual Studio belgelerine](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
)başvurun. En iyi sonuçlar için, "tam", "taşınabilir" veya "katıştırılmış" kullanmanızı öneririz.

### <a name="optimized-builds"></a>İyileştirilmiş derlemeler
Bazı durumlarda, JıT derleyicisi tarafından uygulanan iyileştirmeler nedeniyle yerel değişkenler yayın yapılarında görüntülenemez.
Ancak, Azure App Services 'de Snapshot Collector koleksiyon planının parçası olan oluşturma yöntemlerini en üst düzeye çıkarlayabilirler.

> [!TIP]
> En iyi duruma getirme desteğini almak için Application Insights site uzantısını App Service ' a yükler.

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanız için Application Insights Snapshot Debugger etkinleştirin:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric Hizmetleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure sanal makineleri ve sanal makine ölçek kümeleri](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Şirket içi sanal veya fiziksel makineler](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Snapshot Debugger ötesinde:
 
* Özel durum beklemeden anlık görüntüler almak için kodunuzda anlık görüntü [noktalarını ayarlayın](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) .
* [Web uygulamalarınızda özel durumları tanılayın,](../../azure-monitor/app/asp-net-exceptions.md) Application Insights için daha fazla özel durum görünür hale getirme açıklanmaktadır.
* [Akıllı algılama](../../azure-monitor/app/proactive-diagnostics.md) , performans bozuklularını otomatik olarak bulur.

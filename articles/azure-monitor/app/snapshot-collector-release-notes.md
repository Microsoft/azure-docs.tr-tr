---
title: Microsoft. ApplicationInsights. SnapshotCollector NuGet paketi için sürüm notları-Application Insights
description: Application Insights Snapshot Debugger tarafından kullanılan Microsoft. ApplicationInsights. SnapshotCollector NuGet paketi için sürüm notları.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 4a787c6e2a9b59874f965a2bbcebea9ce02d8082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97093269"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Microsoft. ApplicationInsights. SnapshotCollector için sürüm notları

Bu makale, Application Insights Snapshot Debugger tarafından kullanılan .NET uygulamaları için Microsoft. ApplicationInsights. SnapshotCollector NuGet paketine yönelik sürümler notlarını içerir.

.NET uygulamaları için Application Insights Snapshot Debugger hakkında daha fazla [bilgi edinin](./snapshot-debugger.md) .

Hata raporları ve geri bildirim için, şurada GitHub 'da bir sorun açın: https://github.com/microsoft/ApplicationInsights-SnapshotCollector

## <a name="release-notes"></a>Sürüm notları

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
1.4.0-pre 'dan bir düzelme bağlantı noktası için bir nokta yayını.
### <a name="bug-fixes"></a>Hata düzeltmeleri
- [Kapatmadan ObjectDisposedException](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097)öğesini onarın.

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Test Azure App Service kodsuz kullanacaksınız iliştirme senaryosunda bulunan bir sorunu gidermek için bir nokta yayını.
### <a name="changes"></a>Değişiklikler
- Netcoreapp 3.0 hedefi artık Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (daha önce >= 2.1.2 'yi) bağımlıdır.

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Birkaç yüksek etki sorunu gidermek için bir nokta yayını.
### <a name="bug-fixes"></a>Hata düzeltmeleri
- 1.3.6 içindeki sembol arama algoritmasını değiştirdiğimiz sırada bozulan, Wwwroot/bin klasöründe sabit PDB keşfi düzeltildi.
- Telemetride sabit gürültülü ExtractWasCalledMultipleTimesException.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Değişiklikler
- SnapshotCollector 'ın netcoreapp 2.0 hedefi Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (yeniden) öğesine bağlıdır. Bu, 1.3.5 Before davranışını geri döndürür. 1.3.6 ' de yükseltmeyi denedik, ancak bazı Azure App Service senaryolarına izin vermez.
### <a name="new-features"></a>Yeni özellikler
- Snapshot Collector APPLICATIONINSIGHTS_CONNECTION_STRING ortam değişkeninden veya TelemetryConfiguration öğesinden ConnectionString 'i okur ve ayrıştırır. Öncelikle bu, anlık görüntü hizmetine bağlanmak için uç noktayı ayarlamak üzere kullanılır. Daha fazla bilgi için bkz. [bağlantı dizeleri belgeleri](./sdk-connection-string.md).
### <a name="bug-fixes"></a>Hata düzeltmeleri
- Net45 hariç tüm hedefler için HttpClient kullanımı, uyumsuz bir SecurityProtocol nedeniyle bazı ortamlarda WebRequest başarısız olduğundan (TLS 1,2 gerektirir).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Değişiklikler
- SnapshotCollector artık tüm hedef çerçeveler için Microsoft. ApplicationInsights >= 2.5.1 'e bağımlıdır. Uygulamanız Microsoft. ApplicationInsights SDK 'sının daha eski bir sürümüne bağımlıysa bu bir büyük değişiklik olabilir.
- Snapshot Uploader içindeki TLS 1,0 ve 1,1 desteğini kaldırın.
- PDB taramaların süresi, 15 dakikadan kısa bir süre içinde artık varsayılan olarak 24 saattir. SnapshotCollectorConfiguration üzerinde Pdbyeniden tarama aralığı aracılığıyla yapılandırılabilir.
- PDB taraması Özyinelemeli yerine yalnızca üst düzey klasörler arar. Bu, sembollerinizin ikili klasörün alt klasörlerinde olması halinde bir büyük değişiklik olabilir.
### <a name="new-features"></a>Yeni özellikler
- Günlük klasörünü eski dosyalarla doldurmaktan kaçınmak için, anlık görüntüyle birlikte bulunan Tuploader 'da günlük döndürme.
- .NET Core 3,0 uygulamaları için en iyileştirme desteği (iliştirme üzerinden ReJIT aracılığıyla).
- NuGet paketine semboller ekleyin.
- Mini dökümler karşıya yüklenirken ek meta veriler ayarlayın.
- SnapshotCollectorTelemetryProcessor 'e başlatılmış bir özellik eklendi. Snapshot Collector tamamen başlatıldığında ve hizmet uç noktasına bağlandığında iptal edilecek olan bir CancellationToken.
- Anlık görüntüler, artık dinamik olarak oluşturulan yöntemlerde özel durumlar için yakalanamaz. Örneğin, Entity Framework sorguları tarafından oluşturulan derlenmiş ifade ağaçları.
### <a name="bug-fixes"></a>Hata düzeltmeleri
- Durum İzleyicisi nedeniyle Snapshot Collector yükleniyor.
- GetSnapshotCollector genişletme yöntemi artık tüm TelemetrySinks arar.
- Desteklenmeyen platformlarda Snapshot Uploader ' i başlatmayın.
- Dinamik metotları iyileştirirken InvalidOperationException 'yi işle (örneğin, Entity Framework)

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Sogeign bulutları için destek ekleme (eski sürümler, sogeign bulutlarında çalışmaz)
- AddSnapshotCollector () kullanılarak anlık görüntü toplayıcısı ekleme daha kolay hale getirilir. [Burada](./snapshot-debugger-appservice.md) daha fazla bilgi bulabilirsiniz.
- Blob bloklarını doğrulamak için FıMMA MD5 ayarını kullanın. Bu, işletim sistemi FIPS uyumlu moda ayarlandığında kullanılamayan varsayılan .NET MD5 şifre algoritmasını önler.
- İşlev çağrılarını en iyi duruma getirirken .NET Framework çerçevelerini yoksayın. Bu davranış, DeoptimizeIgnoredModules yapılandırma ayarı tarafından denetlenebilir.
- Birden `DeoptimizeMethodCount` fazla işlev çağrısının en iyi duruma getirilmesini sağlayan yapılandırma ayarı ekleyin. Buradan daha fazla bilgi

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Yapılandırılmış Izleme anahtarlarına izin verin.
- Anlık görüntüyle devam et Tuploader sağlamlık-eski Uploader günlükleri taşınamadığından bile başlatmaya devam edin.
- SnapshotUploader.exe hemen çıktığında ek telemetri raporlamaya yeniden etkinleştirildi (1.3.3 içinde devre dışı bırakıldı).
- İç Telemetriyi kolaylaştırın.
- _Deneysel Özellik_: ekleme noktası toplama planları: "anlık görüntü Tonsorumlusu" ekleyin. Daha fazla bilgi [burada](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)bulunabilir.

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- .NET Core uygulamaları için anlık görüntüleri karşıya yüklemeden SnapshotUploader.exe neden olan hata düzeltildi.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Deneysel Özellik_: snappoint koleksiyon planları. Daha fazla bilgi [burada](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)bulunabilir.
- Çalışma zamanı, SnapshotCollector 'ın yüklü olduğu AppDomain 'i kaldırdığında işlemin çıkış için beklemek yerine, SnapshotUploader.exe çıkılacak. Bu, IIS 'de barındırılırken toplayıcı güvenilirliğini geliştirir.
- Aynı Izleme anahtarını kullanan birden çok SnapshotCollector örneğine izin vermek için yapılandırma ekleyin: ShareUploaderProcess (varsayılan olarak `true` ).
- SnapshotUploader.exe hemen çıktığında ek telemetri bildirin.
- SnapshotUploader.exe disk yazma için gereken destek dosyası sayısı azalır.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- RtlCloneUserProcess API 'SI ile anlık görüntü toplama desteğini kaldırın ve yalnızca Psscapturesnapsnapshots API 'sini destekler.
- 1 ' den 3 ' e kadar anlık görüntünün kaç dakika içinde yakalanabileceğini varsayılan sınırı artırın.
- SnapshotUploader.exe TLS 1,1 ve 1,2 anlaşmasına izin ver
- Anlık rapor Tuploader bir uyarı veya hata günlüğü oluştururken ek telemetri bildirin
- Arka uç hizmetine günlük kotaya ulaşıldığında anlık görüntü almayı Durdur (günde 50 anlık görüntü)
- İki örneğe aynı anda çalışmasına izin vermek için SnapshotUploader.exe ek denetim ekleyin.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Değişiklikler
- .NET Framework hedefleyen uygulamalar için Snapshot Collector artık Microsoft. ApplicationInsights sürüm 2.3.0 veya üstüne bağımlıdır.
2.2.0 veya üzeri olmak için kullanılır.
Bu, çoğu uygulama için bir sorun olmadığını düşüntik, ancak bu değişikliğin en son Snapshot Collector kullanmanızı engelliyorsa haberdar olun.
- Başarısız karşıya yüklemeleri yeniden denerken anlık görüntü yükleyicisi 'nde üstel geri alma gecikmeleri kullanın.
- Telemetriyi daha güvenilir olarak raporlamak için ServerTelemetryChannel (varsa) kullanın.
- Bağımlılık izleme tarafından yoksayılması için Snapshot Debugger hizmetine ilk bağlantıda ' Sdkınternaloperationsmonitor ' kullanın.
- Snapshot Debugger hizmetine ilk bağlantı çevresinde Telemetriyi geliştirme.
- İçin ek telemetri bildirin:
  - Azure App Service sürümü.
  - Azure işlem örnekleri.
  - Kapsayıcıları.
  - Azure Işlevi uygulaması.
### <a name="bug-fixes"></a>Hata düzeltmeleri
- Sorun sayacı sıfırlama aralığı 24 güne ayarlandığında, bunu 24 saat olarak yorumlayın.
- Anlık görüntü elden atılırken bir özel durum oluşursa, anlık görüntü yükleyicisi 'nin yeni anlık görüntüleri işlemeyi durdurulacağı bir hata düzeltildi.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Snapshot Uploader ikilileriyle güçlü ad imzalamayı düzeltir.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Değişiklikler
- Anlık dosya Tuploader (64). exe için gereken dosyalar artık ana DLL 'ye kaynaklar olarak katıştırılır. Yani, SnapshotCollectorFiles klasörünün artık oluşturulmayacağı, derleme ve dağıtımı basitleştirecek ve Çözüm Gezgini üzerindeki dağınıklığı azaltmaktadır. Dosyanızdaki değişiklikleri gözden geçirmek için yükseltme yaparken dikkatli olmanız gerekebilir `.csproj` . `Microsoft.ApplicationInsights.SnapshotCollector.targets`Dosya artık gerekli değildir.
- Provideanonymoustelemetri false olarak ayarlanmış olsa bile telemetri Application Insights kaynağına kaydedilir. Bu, Azure portal bir sistem durumu denetimi özelliği uygulayabiliriz. Provideanonymoustelemetrisi yalnızca ürün desteği ve geliştirmesi için Microsoft 'a gönderilen telemetrisi etkiler.
- TempFolder veya ShadowCopyFolder ortam değişkenlerine yönlendirildiğinde, bu ortam değişkenleri ayarlanana kadar toplayıcıyı boşta tutun.
- Bir ara sunucu üzerinden Internet 'e bağlanan uygulamalar için Snapshot Collector artık tüm proxy ayarlarını otomatik olarak yeniden dener ve SnapshotUploader.exe üzerine geçirebilir.
- SnapshotUplaoder işleminin önceliğini düşürün (mümkün olduğunda). Bu öncelik, ılowprioırtysnapshotuploader seçeneği aracılığıyla geçersiz kılınabilir.
- Snapshot Collector programlı olarak yapılandırmak istediğiniz senaryolar için TelemetryConfiguration üzerinde bir GetSnapshotCollector genişletme yöntemi eklendi.
- Müşteriye yönelik telemetrideki Application Insights SDK sürümünü (uygulama sürümü yerine) ayarlayın.
- İlk sinyal olayını iki dakika sonra gönderin.
### <a name="bug-fixes"></a>Hata düzeltmeleri
- Özel durumların null veya sabit veri sözlükleri olduğunda NullReferenceException öğesini düzeltir.
- Paylaşım ihlali olursa, Uploader öğesinde PDB eşleştirmeyi birkaç kez daha deneyin.
- Birden çok iş parçacığı başlangıçta telemetri işlem hattına çağırdığında yinelenen Telemetriyi düzeltir.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Değişiklikler
- XML belgesi Açıklama dosyaları artık NuGet paketine dahil edilmiştir.
- `System.Exception`Gürültülü bir özel duruma sahip olduğunuzu bildiğiniz ve bunun için anlık görüntü oluşturmamasını istediğiniz senaryolar için bir Excludefromsnapsho, genişletme yöntemi eklendi.
- Isenabledwhenprofilleme yapılandırma özelliği eklendi, varsayılan değer true 'dur. Bu, Application Insights Profiler ayrıntılı bir koleksiyon gerçekleştirmede anlık görüntü oluşturma 'nın geçici olarak devre dışı bırakıldığı önceki sürümlerden bir değişikdir. Eski davranış bu özellik false olarak ayarlanarak kurtarılabilir.
### <a name="bug-fixes"></a>Hata düzeltmeleri
- SnapshotUploader64.exe düzgün şekilde imzalayın.
- Telemetri işlemcisinin çift başlatılmasına karşı koruyun.
- Birden çok işlem hattına sahip uygulamalarda telemetri için çift günlüğü engelleyin.
- Bir koleksiyon planının sona erme saatiyle bir hatayı düzeltir, bu da 24 saat sonra anlık görüntüleri engelleyebilir.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
Bu sürümdeki en büyük değişiklik (Bu nedenle yeni bir alt sürüm numarasına git), anlık görüntü oluşturma ve işleme işlem hattının bir yeniden yazma işlemi olur. Önceki sürümlerde, bu işlev yerel kodda (üretim kesme noktaları *. dll ve anlık görüntüyle daha eski*. exe) uygulanmıştır. Yeni uygulama, P/Invoke ile yönetilen tüm koddur. Yeni işlem hattını kullanan bu ilk sürüm için özgün davranıştan en çok bir şekilde hiç bir şekilde bağlanmadınız. Yeni uygulama daha iyi hata raporlaması sağlar ve gelecekteki geliştirmeler için bize ayarlar.

### <a name="other-changes-in-this-version"></a>Bu sürümdeki diğer değişiklikler
- MinidumpUploader.exe, SnapshotUploader.exe (veya SnapshotUploader64.exe) olarak yeniden adlandırıldı.
- İstekleri geri Iyileştirmek/yeniden Iyileştirmek için zamanlama telemetrisi eklendi.
- Mini döküm yüklemeleri için gzip sıkıştırması eklendi.
- Pdb 'leri kilitli olduğu bir sorun düzeltildi ve site yükseltmesini önler.
- Gölge kopyalama sırasında özgün klasör adını (SnapshotCollectorFiles) günlüğe kaydedin.
- OOM nedeniyle site yeniden başlatılmasını engellemek için 64 bitlik işlemlere yönelik bellek sınırlarını ayarlayın.
- Hala devre dışı bırakıldıktan sonra bile anlık görüntülerin toplandığı bir sorunu düzeltir.
- Sinyal olaylarını müşterinin AI kaynağına kaydedin.
- Sorun KIMLIĞINDEN "kaynak" öğesini kaldırarak anlık görüntü hızını artırın.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Değişiklikler
Genişletmüş kullanım telemetrisi
- .NET sürümünü ve işletim sistemini Algıla ve raporla
- Ek Azure ortamlarını (bulut hizmeti, Service Fabric) Algıla ve raporla
- Sinyal telemetrisi içinde özel durum ölçümlerini kaydedin ve bildirin (1. şans özel durumlarının sayısı ve TrackException çağrılarının sayısı).
### <a name="bug-fixes"></a>Hata düzeltmeleri
- SqlException 'nın, iç özel durumun (Win32Exception) oluşturulduğu doğru işlemesi.
- Sembol klasörlerinde sondaki boşlukları Kırp, bu, komut satırı bağımsız değişkenlerinin hatalı ayrıştırmasına neden olan MinidumpUploader.
- Snapshot Debugger aracısının uç noktasına yönelik başarısız bağlantıların sonsuz şekilde yeniden deneneceğini önleyin.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Değişiklikler
- Ana bilgisayar belleği koruması eklendi. Bu özellik, konak makinesinin belleğindeki etkisini azaltır.
- Azure portal anlık görüntü görüntüleme deneyimini geliştirir.
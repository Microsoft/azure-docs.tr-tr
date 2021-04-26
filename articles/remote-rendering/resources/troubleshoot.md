---
title: Sorun giderme
description: Azure uzaktan Işleme ilgili sorun giderme bilgileri
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 8f0fb9ab5c53c3fd1bfb32ac7b112a116301cba7
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575352"
---
# <a name="troubleshoot"></a>Sorun giderme

Bu sayfada, Azure uzaktan Işleme ile müdahale eden genel sorunlar ve bunları çözme yolları listelenmektedir.

## <a name="cant-link-storage-account-to-arr-account"></a>Depolama hesabı ARR hesabına bağlanamıyor

Bazen [bir depolama hesabının bağlanması](../how-tos/create-an-account.md#link-storage-accounts) sırasında uzaktan işleme hesabı listelenmez. Bu sorunu onarmak için Azure portal ARR hesabına gidin ve soldaki **Ayarlar** grubunda bulunan **kimlik** ' i seçin. **Durumun** **Açık** olarak ayarlandığından emin olun.
![Unity çerçevesi hata ayıklayıcısı](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>İstemci sunucuya bağlanamıyor

Güvenlik duvarlarınızın (cihazda, yönlendiricilerin içinde, vb.) [sistem gereksinimlerinde](../overview/system-requirements.md#network-firewall)belirtilen bağlantı noktalarını engellemediğinden emin olun.

## <a name="error-disconnected-videoformatnotavailable"></a>Hata ' `Disconnected: VideoFormatNotAvailable` '

GPU 'nun donanım video kodunu çözmeyi destekleyip desteklemediğini denetleyin. Bkz. [GELIŞTIRME bilgisayarı](../overview/system-requirements.md#development-pc).

İki GPU içeren bir dizüstü bilgisayarda çalışıyorsanız, varsayılan olarak üzerinde çalıştırdığınız GPU, donanım video kod çözme işlevselliği sağlamaz. Bu durumda, uygulamanızı diğer GPU 'YU kullanmaya zorlamaya çalışın. Bu durum genellikle GPU sürücü ayarlarında mümkündür.

## <a name="retrieve-sessionconversion-status-fails"></a>Oturum/dönüştürme durumunu alma başarısız

REST API komutlarının çok sık gönderilmesi sunucunun başarısız olmasına ve sonunda hata döndürmesine neden olur. Daraltma çalışmasının http durum kodu 429 ' dir ("çok fazla istek"). Thumb kuralı olarak, **sonraki çağrılar arasında 5-10 saniyelik** bir gecikme olmalıdır.

Not Bu sınır yalnızca doğrudan çağrıldığında, ancak, ya da gibi C#/C + + ortaklarınıza REST API çağrıları etkilemez `Session.GetPropertiesAsync` `Session.RenewAsync` `Frontend.GetAssetConversionStatusAsync` .

Sunucu tarafı azaltmasına karşılaşıyorsanız, çağrıları daha az sıklıkta yapmak için kodu değiştirin. Sunucu, dakikada bir dakika sonra kodu yeniden çalıştırmak güvenli olduğundan, azaltma durumunu her dakikada sıfırlar.

## <a name="h265-codec-not-available"></a>H265 codec bileşeni kullanılamıyor

Sunucunun bir hatayla bağlanmayı reddedebileceği iki neden vardır `codec not available` .

**H265 codec bileşeni yüklü değil:**

İlk olarak, sistem gereksinimlerinin [yazılım](../overview/system-requirements.md#software) bölümünde belirtildiği gibi **HEVC video uzantılarını** yüklediğinizden emin olun.

Hala sorun yaşıyorsanız, grafik kartınızın h265 desteklediğinden ve en son grafik sürücüsünün yüklü olduğundan emin olun. Satıcıya özgü bilgiler için sistem gereksinimlerinin [GELIŞTIRME bilgisayarı](../overview/system-requirements.md#development-pc) bölümüne bakın.

**Codec bileşeni yüklendi, ancak kullanılamıyor:**

Bu sorunun nedeni, dll 'lerde yanlış bir güvenlik ayarıdır. Bu sorun, h265 ile kodlanan videoları izlerken bildirim yapmaz. Codec bileşeninin yeniden yüklenmesi sorunu çözmez. Bunun yerine, aşağıdaki adımları uygulayın:

1. **Yönetici haklarına sahip bir PowerShell** açın ve çalıştırın

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Bu komut, `InstallLocation` codec bileşenini şöyle bir şekilde çıktı almalıdır:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Bu klasörü Windows Gezgini 'nde aç
1. **X86** ve **x64** alt klasörü olmalıdır. Klasörlerden birine sağ tıklayın ve **Özellikler** ' i seçin
    1. **Güvenlik** sekmesini seçin ve **Gelişmiş** Ayarlar düğmesine tıklayın
    1. **Sahip** için **Değiştir** 'e tıklayın
    1. Metin alanına **yönetici** yazın
    1. **Adları denetle** ve **Tamam 'a** tıklayın
1. Diğer klasör için yukarıdaki adımları yineleyin
1. Ayrıca, yukarıdaki adımları her iki klasörün içindeki her DLL dosyasında da yineleyin. Tamamen dört dll olmalıdır.

Ayarların artık doğru olduğunu doğrulamak için, bunu dört dll 'nin her biri için yapın:

1. **Özellikleri seçin > güvenlik > Düzenle**
1. Tüm **gruplar/kullanıcılar** listesini okuyun ve her birinin **Oku & Çalıştır** sağ ayarlanmış olduğundan emin olun ( **izin verme** sütunundaki onay işareti oluşturulmalıdır)

## <a name="low-video-quality"></a>Düşük video kalitesi

Video kalitesi ağ kalitesiyle veya eksik h265 video codec bileşeniyle karşı tehlikeye girebilir.

* [Ağ sorunlarını belirlemek](#unstable-holograms)için adımlara bakın.
* En son grafik sürücüsünü yüklemek için [sistem gereksinimleri](../overview/system-requirements.md#development-pc) bölümüne bakın.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>MRC ile kaydedilen video, canlı deneyim kalitesini yansıtmaz

Bir video, [karma gerçeklik yakalama (MRC)](/windows/mixed-reality/mixed-reality-capture-for-developers)aracılığıyla HoloLens 'te kaydedilebilir. Ancak, sonuçta elde edilen videonun iki nedenden dolayı canlı deneyiminden daha kötü bir kalite vardır:
* Video kare hızı 60 Hz 'in aksine 30 Hz 'e göre belirlenir.
* Video görüntüleri, [geç aşama yeniden projeksiyonu](../overview/features/late-stage-reprojection.md) işleme adımını etkilemez, bu nedenle video choppier gibi görünür.

Her ikisi de kayıt tekniğinin devralınmış kısıtlamalarıdır.

## <a name="black-screen-after-successful-model-loading"></a>Başarılı model yüklendikten sonra siyah ekran

İşleme çalışma zamanına bağlıysanız ve bir modeli başarıyla yüklediyseniz, ancak daha sonra yalnızca bir siyah ekran görürseniz, bu birkaç farklı neden olabilir.

Daha ayrıntılı bir analiz yapmadan önce aşağıdaki şeyleri test etmenizi öneririz:

* H265 codec bileşeni yüklendi mi? H264 codec bileşenine geri dönüş olması gerekse de, bu geri dönüş düzgün şekilde çalışmayan durumlar görülmüştür. En son grafik sürücüsünü yüklemek için [sistem gereksinimleri](../overview/system-requirements.md#development-pc) bölümüne bakın.
* Unity projesi kullanırken Unity 'yi kapatın, proje dizinindeki geçici *kitaplığı* ve *obj* klasörlerini silin ve projeyi yeniden yükleyin/derleyin. Bazı durumlarda önbelleğe alınmış veriler, örnek olarak belirgin bir nedenle düzgün şekilde çalışmayabilir.

Bu iki adım yardımcı olduysa, video çerçevelerinin istemci tarafından alınıp alınmadığını öğrenmek gerekir. Bu, [sunucu tarafı performans sorguları](../overview/features/performance-queries.md) bölümünde açıklandığı gibi programlı bir şekilde sorgulanabilir. , `FrameStatistics struct` Kaç video karesinin alındığını gösteren bir üyeye sahiptir. Bu sayı 0 ' dan büyükse ve zaman içinde arttırıldığında, istemci sunucudan gerçek video çerçevelerini alır. Sonuç olarak, istemci tarafında bir sorun olması gerekir.

### <a name="common-client-side-issues"></a>Ortak istemci tarafı sorunları

**Model, seçilen VM 'nin sınırlarını, özellikle de en yüksek sayıda poligonu aşıyor:**

Belirli [sunucu boyutu sınırlarına](../reference/limits.md#overall-number-of-polygons)bakın.

**Model, bu kameranın Frustum içinde değil:**

Çoğu durumda, model doğru şekilde görüntülenir, ancak kameranın dışında bulunur. Yaygın bir nedenden dolayı modelin, kameranın en kırpma düzlemine göre kırpılması için, modelin en dışı bir özete aktarılmasının yaygın bir nedeni vardır. Modelin sınırlayıcı kutusunun programlı bir şekilde sorgulanmasına ve kutuyu bir satır kutusu olarak görselleştirmeye veya değerlerini hata ayıklama günlüğüne yazdırmaya yardımcı olur.

Dönüştürme işleminin yanı sıra, dönüştürülmüş modelle birlikte bir [çıktı JSON dosyası](../how-tos/conversion/get-information.md) da oluşturur. Model konumlandırma sorunlarını ayıklamak için, `boundingBox` [outputstatıstıcs bölümündeki](../how-tos/conversion/get-information.md#the-outputstatistics-section)girişe göz uydurulmaya değecektir:

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Sınırlayıcı kutu, ölçüm olarak 3B alanında bir `min` ve konumu olarak tanımlanır `max` . Bu nedenle 1000,0 koordinatı, kaynağın kaynağın 1 kilometreden fazla olması anlamına gelir.

Bu sınırlama kutusuyla ilgili olarak görünmeyen geometriye yol açabilecek iki sorun olabilir:
* **Kutu**, en çok orta basıyor olabilir, bu nedenle nesne, uzak düzlem kırpması nedeniyle tamamen kırpıldı. `boundingBox`Bu örnekte yer alan değerler şöyle görünür: `min = [-2000, -5,-5], max = [-1990, 5,5]` , burada örnek olarak x ekseninde büyük bir konum kullanılıyor. Bu tür bir sorunu çözmek için `recenterToOrigin` [model dönüştürme yapılandırmasındaki](../how-tos/conversion/configure-model-conversion.md)seçeneği etkinleştirin.
* **Kutu ortalanmış, ancak çok büyük boyutlu siparişler olabilir**. Bu, kameranın modelin merkezinde başladığı anlamına gelir; bu, geometrisi her yönde kırpılır. `boundingBox`Bu örnekte tipik değerler şuna benzer: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . Bu tür bir sorunun nedeni genellikle bir birim ölçek uyuşmazlığıdır. Dengelemek için, [dönüştürme sırasında bir ölçekleme değeri](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) belirtin veya kaynak modeli doğru birimlerle işaretleyin. Ölçek, çalışma zamanında model yüklenirken kök düğümüne da uygulanabilir.

**Unity oluşturma işlem hattı işleme kancalarını içermez:**

Azure uzaktan Işleme, video ile çerçeve oluşturmayı ve yeniden projeksiyonu yapmak için Unity işleme ardışık düzenine takılır. Bu kancalarının mevcut olduğunu doğrulamak için menüsünü açın *:::no-loc text="Window > Analysis > Frame debugger":::* . Bunu etkinleştirin ve `HolographicRemotingCallbackPass` ardışık düzende ' ın iki girişi olduğundan emin olun:

![Unity işleme işlem hattı](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Dama tahtası stili, model yüklendikten sonra işlenir

İşlenmiş resim şuna benziyorsa: ![ ekran görüntüsü, bir Araçlar menüsü ile siyah ve beyaz karelerin bir kılavuzunu gösterir.](../reference/media/checkerboard.png)
ardından, işleyici [Standart yapılandırma boyutu için Çokgen sınırlarını](../reference/vm-sizes.md)isabetler. Azaltmak için, **Premium** yapılandırma boyutuna geçin veya görünür çokgenler sayısını azaltın.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Unity 'de işlenen görüntü, baş aşağı

[Unity öğreticisini izlediğinizden emin olun: uzak modelleri](../tutorials/unity/view-remote-models/view-remote-models.md) tam olarak görüntüleyin. Ters bir görüntü, Unity 'nin bir ekran oluşturma hedefi oluşturmak için gerekli olduğunu gösterir. Bu davranış Şu anda desteklenmiyor ve HoloLens 2 ' de büyük bir performans etkisi oluşturuyor.

Bu sorunun nedenleri MSAA, HDR veya post işlemini etkinleştirme olabilir. Düşük kaliteli profilin seçildiğinden ve Unity 'de varsayılan olarak ayarlandığından emin olun. Bunu yapmak için *> proje ayarlarını Düzenle... > kalite*' ye gidin.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Uzaktan Işleme API 'sini kullanan Unity kodu derlenmiyor

### <a name="use-debug-when-compiling-for-unity-editor"></a>Unity Düzenleyicisi için derlerken hata ayıklamayı kullan

Unity çözümünün *derleme türünü* **hata ayıklaması** için değiştirin. Unity düzenleyicisinde ARR testi yaparken, tanımlama `UNITY_EDITOR` yalnızca ' Debug ' yapılarında kullanılabilir. Bu, [dağıtılan uygulamalar](../quickstarts/deploy-to-hololens.md)için kullanılan yapı türü ile ilgili değildir ve burada ' Release ' derlemelerini tercih etmelisiniz.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>HoloLens 2 için Unity örnekleri derlenirken derleme sorunları

HoloLens 2 için Unity örneklerini (hızlı başlangıç, ShowCaseApp,..) derlemeye çalışırken silatik arızalarıyla karşılaştık. Visual Studio şikayet eden bazı dosyaları burada kopyalayamıyorum. Bu soruna ulaşırsanız:
* Tüm geçici Unity dosyalarını projeden kaldırın ve yeniden deneyin. Diğer bir deyişle, Unity 'yi kapatın, proje dizinindeki geçici *kitaplığı* ve *obj* klasörlerini silip projeyi yeniden yükleyin veya oluşturun.
* Kopyalama adımı bazen uzun dosya adlarıyla sorunlar halinde çalıştırıldıklarından, projelerin olabildiğince kısa yola sahip bir diskte dizinde bulunduğundan emin olun.
* Bu yardım yoksa, MS Sense kopyalama adımını kesintiye uğratır. Bir özel durum ayarlamak için komut satırından bu kayıt defteri komutunu çalıştırın (yönetici hakları gerektirir):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>AudioPluginMsHRTF.dll eksik olduğundan Unity projeleri için Arm64 derlemeleri başarısız oluyor

`AudioPluginMsHRTF.dll`For Arm64, 3.0.1 sürümündeki *Windows Mixed Reality* paketine *(com. Unity. xr. windowsmr. metro)* eklenmiştir. Unity paket Yöneticisi aracılığıyla sürüm 3.0.1 veya daha yeni bir sürümün yüklü olduğundan emin olun. Unity menü çubuğundan *pencere > Paket Yöneticisi* ' ne gidin ve *Windows Mixed Reality* paketini bulun.

## <a name="native-c-based-application-does-not-compile"></a>Yerel C++ tabanlı uygulama derlenmiyor

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>UWP uygulaması veya dll için ' kitaplık bulunamadı ' hatası

C++ NuGet paketinin içinde, `microsoft.azure.remoterendering.Cpp.targets` hangi ikilinin kullanılacağını tanımlayan dosya dosyası vardır. Belirlemek için `UWP` Dosya denetimi içindeki koşulları `ApplicationType == 'Windows Store'` . Bu nedenle, bu türün projede ayarlanmış olması gerekir. Bu, Visual Studio 'nun proje Sihirbazı aracılığıyla bir UWP uygulaması veya DLL oluştururken büyük/küçük harf olmalıdır.

## <a name="unstable-holograms"></a>Dengesiz Hologragram

İşlenen nesnelerin baş hareketlerle birlikte taşınması gibi görünse de, *geç aşama yeniden projeksiyonu* (LSR) ile ilgili sorunlarla karşılaşıyor olabilirsiniz. Böyle bir duruma yaklaşıma ilişkin yönergeler için, [geç aşama yeniden projeksiyonunun](../overview/features/late-stage-reprojection.md) bölümüne bakın.

Kararsız hologragram (Wobbling, warping, su ya da atlama hologramlar) için başka bir neden, ağ bağlantısı yetersizliğinde veya çok yüksek gecikme süresine sahip olabilir. Ağ bağlantınızın kalitesi için iyi bir gösterge, [performans istatistikleri](../overview/features/performance-queries.md) değeridir `ServiceStatistics.VideoFramesReused` . Yeniden kullanılan çerçeveler, yeni video çerçevesi kullanılabilir olmadığından (örneğin, paket kaybı nedeniyle veya ağ gecikmede Çeşitlemeler nedeniyle), eski video çerçevesinin istemci tarafında yeniden kullanılması gereken durumları gösterir. `ServiceStatistics.VideoFramesReused`Sıfırdan sık büyükse bu bir ağ sorununu gösterir.

Bakmak için başka bir değer de vardır `ServiceStatistics.LatencyPoseToReceiveAvg` . Sürekli olarak 100 ms 'nin altında olması gerekir. Daha yüksek değerleri görmek çok uzakta olan bir veri merkezine bağlı olduğunu gösterebilir.

Olası azaltmaları bir liste için bkz. [ağ bağlantısı yönergeleri](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="z-fighting"></a>Z-Fighting

ARR, [kele hafifletme işlevselliği](../overview/features/z-fighting-mitigation.md)sunurken, z-Fighting, sahnede görünmeye devam edebilir. Bu, kalan sorunları gidermeye yönelik bu kılavuzda hata giderme.

### <a name="recommended-steps"></a>Önerilen adımlar

Z-Fighting 'i azaltmak için aşağıdaki iş akışını kullanın:

1. Sahneye varsayılan ayarlarla test edin (z-Fighting üzerinde risk azaltma)

1. [API 'si](../overview/features/z-fighting-mitigation.md) ile z mücadele riskini devre dışı bırakma 

1. Kameranın yakınında ve en solundaki düzlemi daha yakın bir aralığa değiştirme

1. Sonraki bölüm aracılığıyla sahnenin sorunlarını giderme

### <a name="investigating-remaining-z-fighting"></a>Kalan z-Fighting araştırma

Yukarıdaki adımlar tükenirse ve kalan z mücadele kabul edilemez ise, z 'nin temel nedeninin araştırılması gerekir. [Z 'nin risk azaltma özelliği sayfasında](../overview/features/z-fighting-mitigation.md)belirtildiği gibi, z mücadele için iki temel neden vardır: derinlik aralığının en sonunda derinlik duyarlık kaybı ve kaar sırasında kesişen yüzeyler. Derinlik duyarlık kaybı matematiksel bir eventuseldir ve yalnızca yukarıdaki 3. adımda azaltılabilir. Coplanar yüzeyleri bir kaynak varlık kusurunu gösterir ve kaynak verilerde daha iyi düzeltilir.

ARR, yüzeylerin z-Fige olup olmadığını belirlemek için bir özelliğe sahiptir: [dama tahtası vurgulama](../overview/features/z-fighting-mitigation.md). Ayrıca, z 'ye ne neden olduğunu görsel olarak belirleyebilirsiniz. Aşağıdaki ilk animasyon, uzaklığa göre derinlemesine bir duyarlık kaybı örneği gösterir ve ikincisi neredeyse coplanar yüzeylerinin bir örneğini gösterir:

![Animasyon, uzaklığa göre derinlemesine bir duyarlık kaybı örneği gösterir.](./media/depth-precision-z-fighting.gif)  ![Animasyon, neredeyse coplanar yüzeylerine bir örnek gösterir.](./media/coplanar-z-fighting.gif)

Nedeni öğrenmek için bu örnekleri z ile karşılaştırın ya da isteğe bağlı olarak bu adım adım iş akışını izleyin:

1. Kamerayı doğrudan yüzeye bakmak için z-Fighting yüzeylerinin üzerine konumlandırın.
1. Kamerayı yavaşça, yüzeylerden uzağa doğru hareket ettirin.
1. Z-Fighting her zaman görünür durumdaysa, yüzeyler kusursuz coplanar. 
1. Z-Fighting çoğu zaman görünür durumdaysa, yüzeyler neredeyse coplanar olur.
1. Z-Fighting yalnızca en başından itibaren görülemiyorsa, nedeni derinlemesine bir duyarlık olmamasıdır.

Coplanar yüzeylerinin çeşitli nedenleri olabilir:

* Bir hata veya farklı iş akışı yaklaşımı nedeniyle bir nesne dışarı aktarma uygulaması tarafından yinelendi.

    İlgili uygulama ve uygulama desteğiyle ilgili bu sorunları denetleyin.

* Yüzeyler yinelenir ve ön yüz ya da arka yüz katmanı kullanan oluşturuculara çift taraflı görünmesi için çevrilmiş.

    [Model dönüştürme](../how-tos/conversion/model-conversion.md) ile içeri aktarma, modelin ana kenar düzeyini belirler. Çift yönlü bir varsayılan olarak kabul edilir. Yüzey, her iki taraftan da fiziksel olarak doğru aydınlatma ile ince bir duvar olarak işlenir. Tek taraflı engeli, kaynak varlığın bayrakları tarafından ima edilebilir veya [model dönüştürme](../how-tos/conversion/model-conversion.md)sırasında açıkça zorlanır. Ayrıca, isteğe bağlı olarak, [tek taraflı mod](../overview/features/single-sided-rendering.md) "normal" olarak ayarlanabilir.

* Nesneler kaynak varlıklarda Kesiştir.

     Nesneleri, bazı yüzeylerinin örtüşmesine yol bir şekilde dönüştürülemez. ARR 'deki içeri aktarılan sahnedeki sahne ağacının parçalarını dönüştürmek bu sorunu da oluşturabilir.

* Yüzeyler, iletişim için, Decal 'ler veya duvarlardaki metinler gibi, dokunarak tam olarak yazılmıştır.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Yerel C++ uygulamalarında çok geçişli stereo işleme kullanan grafik yapıtları

Bazı durumlarda, yerel içerik (sol ve sağ gözle ayrı geçişlerde işleme) için çok taramalı bir stereo işleme modu kullanan özel yerel C++ [**uygulamaları, bir**](../concepts/graphics-bindings.md#render-remote-image) sürücü hatasını tetikleyebilir. Hata belirleyici olmayan rasterleştirme görünmelere neden olur, tek tek üçgenler veya yerel içeriğin üçgeninin bölümlerinin rastgele kaybolması. Performans nedenleriyle, örneğin **SV_RenderTargetArrayIndex** kullanarak, daha modern bir tek geçişli stereo işleme tekniğinden yerel içerik işlemek de önerilir.

## <a name="conversion-file-download-errors"></a>Dönüştürme dosyası Indirme hataları

Dönüştürme hizmeti, Windows ve hizmet tarafından uygulanan yol uzunluğu sınırları nedeniyle blob depolamadan dosya indirme hatalarıyla karşılaşıyor olabilir. Blob depolamadaki dosya yolları ve dosya adları 178 karakteri aşmamalıdır. Örneğin `blobPrefix` `models/Assets` , 13 karakter olan bir.

`models/Assets/<any file or folder path greater than 164 characters will fail the conversion>`

Dönüştürme hizmeti, `blobPrefix` yalnızca dönüştürmede kullanılan dosyalar değil, altında belirtilen tüm dosyaları indirir. Soruna neden olan dosyalar/klasör bu durumlarda daha az belirgin olabilir. bu sayede, altındaki depolama hesabında bulunan her şeyi denetlemek önemlidir `blobPrefix` . İndirilen özellikler için aşağıdaki örnek girişlere bakın.
``` json
{
  "settings": {
    "inputLocation": {
      "storageContainerUri": "https://contosostorage01.blob.core.windows.net/arrInput",
      "blobPrefix": "models/Assets",
      "relativeInputAssetPath": "myAsset.fbx"
    ...
  }
}
```

```
models
├───Assets
│   │   myAsset.fbx                 <- Asset
│   │
│   └───Textures
│   |       myTexture.png           <- Used in conversion
│   |
|   └───MyFiles
|          myOtherFile.txt          <- File also downloaded under blobPrefix      
|           
└───OtherFiles
        myReallyLongFileName.txt    <- Ignores files not under blobPrefix             
```
## <a name="next-steps"></a>Sonraki adımlar

* [Sistem gereksinimleri](../overview/system-requirements.md)
* [Ağ gereksinimleri](../reference/network-requirements.md)
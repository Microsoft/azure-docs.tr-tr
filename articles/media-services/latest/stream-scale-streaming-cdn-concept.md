---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Zorunlu alanlar. Aka.ms/skyeye/meta hakkında daha fazla bilgi için bkz..
Başlık: CDN tümleştirmesi ile içerik akışı: Azure Media Services açıklaması: CDN tümleştirmesiyle akış içeriği ve önceden getirme ve Origin-Assist CDN-önceden getirme hakkında bilgi edinin.
Hizmetler: Media-Services belgetationcenter: ' ' Yazar: ınridatmicrosoft Manager: femıla Düzenleyicisi: ' ' MS. Service: Media-Services MS. Workload: MS. Topic: kavramsal MS. Date: 08/31/2020 MS. Author: inhenkel
---

# <a name="stream-content-with-cdn-integration"></a>CDN tümleştirmesi ile içerik akışı

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Content Delivery Network (CDN), dünya genelindeki stratejik olarak yerleştirilen fiziksel düğümlerde içeriği önbelleğe alarak, yüksek bant genişliği içeriğinin hızlı bir şekilde kullanıcılara teslimi konusunda geliştiricilere genel bir çözüm sunar.  

CDN, her codec için bir Media Services [akış uç noktası (başlangıç)](stream-streaming-endpoint-concept.md) , her akış protokolü, her bir bir kapsayıcı biçimi başına ve ŞIFRELEME/DRM başına bir akış uç noktası (Origin) akışı Her codec-akış protokolü birleşimi için-bit hızı-şifreleme, ayrı bir CDN önbelleği olacaktır.

Video parçası önbelleğe alındığı sürece popüler içerik doğrudan CDN önbelleğinden sunulacaktır. Tam olarak aynı şeyi izleyen çok sayıda kişi olduğu için canlı içeriğin önbelleğe alınması olasıdır. Çok daha popüler ve bazıları olmayan bazı içeriklere sahip olabileceğinden isteğe bağlı içerik biraz karmaşık olabilir. Bunlardan hiçbirinin popüler olduğu milyonlarca video varlığınız varsa (yalnızca bir veya iki Görüntüleyici), ancak tüm farklı videoları izlerken binlerce insan varsa, CDN çok daha az etkili hale gelir.

Ayrıca, uyarlamalı akışın nasıl çalıştığını göz önünde bulundurmanız gerekir. Her bir video parçası kendi varlığı olarak önbelleğe alınır. Örneğin, belirli bir videonun ilk kez nasıl izlenen hakkında düşünün. Görüntüleyici burada yalnızca birkaç saniye izlemeyi atlar ve burada yalnızca, izlenen kişilerin CDN 'de önbelleğe alınması ile ilişkili video parçaları vardır. Uyarlamalı akış sayesinde genellikle 5 ile 7 arasında farklı bit fiyatları vardır. Bir kişi bir bit hızı izlerken ve başka bir kişi farklı bir bit hızı izlerse, her biri CDN 'de ayrı olarak önbelleğe alınır. İki kişi aynı bit hızını izliyor olsa da, farklı protokollerde akış olabilir. Her protokol (HLS, MPEG-DASH, Kesintisiz Akış) ayrı olarak önbelleğe alınır. Böylece her bit hızı ve protokol ayrı olarak önbelleğe alınır ve yalnızca istenen video parçaları önbelleğe alınır.

Test ortamı hariç, hem standart hem de Premium akış uç noktaları için CDN 'nin etkinleştirilmesini öneririz. Her akış uç noktası türü, desteklenen farklı bir üretilen iş sınırına sahiptir.
Bir akış uç noktası tarafından desteklenen en fazla eşzamanlı akış sayısı için kesin bir hesaplama yapmak zordur, çünkü dikkate almanız gereken çeşitli faktörler vardır. Bu modüller şunlardır:

- Akış için kullanılan maksimum bit hızı
- Oynatıcı ön arabelleği ve anahtarlama davranışı. Oyuncular, bir kaynaktan kesimleri patlama ve Uyarlamalı bit hızı geçişini hesaplamak için yük hızını kullanmanıza çalışır. Akış uç noktası doygunluğu yakınsa, yanıt süreleri farklılık gösterebilir ve oyuncular daha düşük kalitede geçişe başlayabilir. Bu, akış uç noktası yürütücülerinin yükünü azaltmakta olduğundan, istenmeyen geçiş Tetikleyicileri oluşturarak daha yüksek kalitede ölçeklendirin.
En yüksek akış uç noktası aktarım hızını alarak ve en yüksek bit hızına (tüm oyuncuların en yüksek bit hızını kullandığı varsayıldığında) en fazla eşzamanlı akışları tahmin etmek güvenlidir. Örneğin, 600 Mbps ile sınırlı standart bir akış uç noktası ve 3Mbp 'nin en yüksek bit hızına sahip olabilirsiniz. Bu durumda, en yüksek bit hızında yaklaşık 200 eşzamanlı akış desteklenir. Ses bant genişliği gereksinimlerinde de etken olduğunu unutmayın. Ses akışı yalnızca 128 KPS konumunda Akış yapabilse de, toplam akış, eş zamanlı akış sayısına göre çarpdığınızda hızlı bir şekilde eklenir.

Bu konu, [CDN tümleştirmesini](#enable-azure-cdn-integration)etkinleştirmeyi tartışır. Ayrıca, önceden getirme (etkin önbelleğe alma) ve [kaynak-yardım CDN-önceden getirme](#origin-assist-cdn-prefetch) kavramını da açıklar.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

- [Akış uç noktası](stream-streaming-endpoint-concept.md) `hostname` ve akış URL 'si, CDN 'yi etkinleştirip etkinleştirmeksizin aynı kalır.
- İçeriğinizi CDN ile veya CDN olmadan test etmek istiyorsanız, CDN etkin olmayan başka bir akış uç noktası oluşturun.

## <a name="enable-azure-cdn-integration"></a>Azure CDN tümleştirmeyi etkinleştir

> [!IMPORTANT]
> Deneme veya öğrenci Azure hesapları için CDN 'yi etkinleştiremezsiniz.
>
> CDN tümleştirmesi, Federal kamu ve Çin bölgeleri dışındaki tüm Azure veri merkezlerinde etkinleştirilir.

CDN etkinken bir akış uç noktası sağlandıktan sonra, akış uç noktasını CDN uç noktası ile eşlemek için DNS güncelleştirme işlemi yapılmadan önce Media Services tanımlı bir bekleme süresi vardır.

Daha sonra CDN 'yi devre dışı bırakmak/etkinleştirmek istiyorsanız, akış uç noktanızın **durdurulmuş** durumda olması gerekir. Akış uç noktası başlatıldıktan sonra, Azure CDN tümleştirmenin etkinleştirilmesi ve değişikliklerin tüm CDN pop 'larda etkin olması dört saate kadar sürebilir. Ancak, akış uç noktasından kesintiler olmadan akış uç noktanızı ve akışı başlatabilirsiniz. Tümleştirme tamamlandıktan sonra, akış CDN 'den dağıtılır. Sağlama süresi boyunca, akış uç noktanız **Başlangıç** durumunda olur ve performans düşüklüğü gözlemleyebilirsiniz.

Standart akış uç noktası oluşturulduğunda, standart Verizon ile varsayılan olarak yapılandırılır. REST API 'Lerini kullanarak Premium Verizon veya standart Akamai sağlayıcılarını yapılandırabilirsiniz.

Azure CDN ile tümleştirme Azure Media Services standart akış uç noktaları için **Verizon 'tan Azure CDN** uygulanır. Premium akış uç noktaları, tüm **Azure CDN fiyatlandırma katmanları ve sağlayıcıları** kullanılarak yapılandırılabilir.

> [!NOTE]
> Azure CDN hakkındaki ayrıntılar için bkz. [CDN 'ye genel bakış](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>DNS değişikliğinin yapıldığını belirleme

Kullanarak bir akış uç noktasında (trafiğin Azure CDN yönlendirilmekte) DNS değişikliği yapıldığını belirleyebilirsiniz <https://www.digwebinterface.com> . Sonuçlarda azureedge.net etki alanı adları görürseniz, trafik artık CDN 'ye işaret ediyor.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

CDN önbelleği, reaktif bir işlemdir. CDN bir sonraki nesnenin isteneceğini tahmin edebildiyse, CDN bir sonraki nesneyi proaktif olarak isteyebilir ve önbelleğe alabilir. Bu işlemle, nesnelerin tümü (veya çoğu) için, performansı artıran bir önbellek okuması elde edebilirsiniz.

Önceden getirme kavramı, olasılığına içindeki "internet Edge" de nesneleri, bu nesnenin Player tarafından istenme süresini azaltarak, bu sayede bu nesneyi yürütücüye sunmaya yönelik süreyi azaltan bir konuma konumlandırır.

Bu hedefe ulaşmak için, bir akış uç noktası (Origin) ve CDN 'nin el ile birkaç yolla çalışması gerekir:

- Media Services kaynağı, bir sonraki nesneyi önceden getirme için CDN 'e bildirmek üzere "zeka" (Origin-yardım) içermelidir.
- CDN, önceden getirme ve önbelleğe alma (CDN-önceden getirme bölümü) yapar. CDN 'nin bir önceden getirme veya düzenli getirme, 404 yanıtlarını işleme ve sonsuz önceden getirme döngüsünden kaçınmak için bir yol olduğunu bildirmek üzere "zeka" sahip olması gerekir.

### <a name="benefits"></a>Avantajlar

*Kaynak-yardım CDN-önceden getirme* özelliğinin avantajları şunları içerir:

- Önceden getirme, kayıttan yürütme sırasında beklenen video segmentlerini önceden konumlandırarak, görüntüleyiciye gecikme süresini azaltarak ve video segmenti karşıdan yükleme sürelerini geliştirerek video kayıttan yürütme kalitesini geliştirir. Bu, daha hızlı video başlangıç zamanına ve düşük yeniden arabelleğe alma oluşumlarına neden olur.
- Bu kavram, genel CDN-Origin senaryosu için geçerlidir ve medya ile sınırlı değildir.
- Akamai bu özelliği [Akamai Cloud embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)öğesine ekledi.

> [!NOTE]
> Bu özellik henüz Media Services akış uç noktası ile tümleşik Akamai CDN için geçerli değildir. Ancak, önceden var olan bir Akamai sözleşmesi olan ve Akamai CDN ile Media Services kaynağı arasında özel tümleştirme gerektiren Media Services müşteriler için kullanılabilir.

### <a name="how-it-works"></a>Nasıl çalışır?

Üst bilgiler için CDN desteği `Origin-Assist CDN-Prefetch` (hem canlı hem de video isteğe bağlı akış için) AKAMAI CDN ile doğrudan sözleşme sahibi olan müşteriler tarafından kullanılabilir. Özelliği, Akamai CDN ve Media Services kaynağı arasındaki aşağıdaki HTTP üst bilgi alışverişlerini içerir:

|HTTP üstbilgisi|Değerler|Gönderen|Alıcı|Amaç|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (varsayılan) veya 0 |CDN|Kaynak|CDN 'nin önceden hazırlık özelliğinin etkin olduğunu göstermek için.|
|`CDN-Origin-Assist-Prefetch-Path`| Örnek: <br/>Parçalar (video = 1400000000, Format = MPD-Time-cmaf)|Kaynak|CDN|CDN 'ye önceden getirme yolu sağlamak için.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (önceden getirme isteği) veya 0 (normal istek)|CDN|Kaynak|CDN 'den gelen isteğin bir önceden getirme olduğunu belirtmek için.|

Üst bilgi değişimi 'nin bir parçasını eylemde görmek için aşağıdaki adımları deneyebilirsiniz:

1. Bir ses veya video segmenti veya parçası için Media Services kaynağına bir istek vermek üzere Postman veya kıvrımlı kullanın. Üst bilgiyi isteğe eklediğinizden emin olun `CDN-Origin-Assist-Prefetch-Enabled: 1` .
2. Yanıtta, üst bilgiyi `CDN-Origin-Assist-Prefetch-Path` değeri olarak göreli bir yol görmeniz gerekir.

### <a name="supported-streaming-protocols"></a>Desteklenen akış protokolleri

`Origin-Assist CDN-Prefetch`Özelliği, canlı ve isteğe bağlı akış için aşağıdaki akış protokollerini destekler:

* HLS v3
* HLS v4
* HLS CMAF
* TIRE (CSF)
* TIRE (CMAF)
* Kesintisiz Akış

### <a name="faqs"></a>SSS

* CDN önceden getirme işlemi 404 olarak bir önceden getirme yolu URL 'SI geçersizse ne olur?

    CDN yalnızca 10 saniye (veya yapılandırılmış diğer değer) için 404 yanıtını önbelleğe alacak.

* İsteğe bağlı bir videonuz olduğunu varsayalım. CDN-önceden getirme etkinse, bu özellik bir istemci ilk video kesimini istediğinde, önceden getirme işlemi aynı bit hızında sonraki tüm video segmentlerini önceden getirme döngüsünü başlatır.

    Hayır, CDN-önceden getirme yalnızca istemci tarafından başlatılan bir istek/yanıt sonrasında yapılır. CDN-önceden getirme, önceden getirme döngüsünden kaçınmak için hiçbir şekilde önceden getirme tarafından tetiklenmez.

* Origin-Assist CDN-Prefetch özelliği her zaman açık mı? Nasıl açılıp kapatılabilir?

    Bu özellik varsayılan olarak kapalıdır. Müşterilerin Akamai API aracılığıyla açık olması gerekir.

* Canlı akış için, sonraki segment veya parça henüz kullanılamıyorsa Origin-Assist ne olur?

    Bu durumda Media Services kaynağı `CDN-Origin-Assist-Prefetch-Path` üst bilgi sağlamaz ve CDN-önceden getirme gerçekleşmeyecektir.

* `Origin-Assist CDN-Prefetch`Dinamik bildirim filtreleriyle nasıl çalışır?

    Bu özellik bildirim filtresinden bağımsız olarak çalışmaktadır. Sonraki parça bir filtre penceresinin dışında olduğunda, ham istemci bildirimine bakarak ve sonra CDN önceden getirme yanıt üst bilgisi olarak döndürüldüğünden URL 'SI yine de bulunur. Bu nedenle CDN, DASH/HLS/Düzgünleştir bildiriminden filtrelenmiş bir parçanın URL 'sini alır. Ancak, oynatıcı bu parçayı getirmek için hiçbir şekilde CDN 'ye bir GET isteği oluşturmaz, çünkü bu parça Player tarafından tutulan DASH/HLS/düzgün bildirimde yer almamaktadır (oyuncu bu parçanın varlığını bilmez).

* DASH/HLS çalma listesi/Düzgünleştir bildirimi önceden getirilsin mi?

    No, DASH MPD, HLS ana oynatma listesi, HLS Variant çalma listesi veya düz bildirim URL 'SI önceden getirme başlığına eklenmez.

* Önceden hazırlık URL 'Leri göreli veya mutlak mı?

    Akamai CDN her ikisine de izin veriyorsa, mutlak URL 'Ler kullanmanın hiçbir avantajı olmadığından Media Services kaynağı yalnızca önceden getirme yolu için göreli URL 'Ler sağlar.

* Bu özellik, DRM korumalı içerikle birlikte çalışıyor mu?

    Evet, bu özellik HTTP düzeyinde çalıştığından, hiçbir segmenti/parçayı çözmez veya ayrıştırır. İçeriğin şifrelenip şifrelenmediği konusunda dikkatli değildir.

* Bu özellik sunucu tarafı ad eklemesi (SSAı) ile çalışıyor mu?
    
    Asıl/ana içerik (ad ekleme öncesi orijinal video içeriği) için geçerlidir, çünkü SSAı kaynak içeriğin zaman damgasını Media Services kaynağından değiştirmemektedir. Bu özelliğin ad içeriğiyle çalışıp çalışmadığını, ad kaynağının kaynak-Yardım ' a destekleyip desteklemediğini belirtir. Örneğin, ad içeriği de Azure Media Services (aynı veya ayrı bir kaynak) içinde barındırılıyorsa, ad içerikleri de önceden getirilir.

* Bu özellik UıHD/HEVC içeriğiyle çalışıyor mu?

    Evet.

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* [Akış uç noktası (başlangıç)](stream-streaming-endpoint-concept.md) belgesini gözden geçirdiğinizden emin olun.
* [Bu depodaki](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) örnek, .NET ile varsayılan akış uç noktasının nasıl başlatılacağını gösterir.

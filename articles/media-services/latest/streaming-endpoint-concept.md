---
title: Azure Media Services 'de akış uç noktaları (başlangıç) | Microsoft Docs
description: Azure Media Services, bir akış uç noktası (Origin), daha fazla dağıtım için doğrudan bir istemci oynatıcı uygulamasına veya bir Content Delivery Network (CDN) içerik teslim edebilen dinamik bir paketleme ve akış hizmetini temsil eder.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd1dc7b55060e8262b300022f5ffd1b4da5f7922
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350353"
---
# <a name="streaming-endpoints"></a>Akış Uç Noktaları 

Microsoft Azure Media Services, bir [akış uç noktası](https://docs.microsoft.com/rest/api/media/streamingendpoints) , canlı ve isteğe bağlı içeriğinizi, ortak akış medyasından birini kullanarak doğrudan bir istemci oynatıcı uygulamasına teslim edebilen bir dinamik (tam zamanında) paketleme ve kaynak hizmeti temsil eder protokoller (HLS veya DASH). Ayrıca, **akış uç noktası** sektör lideri drms için dinamik (tam zamanında) şifreleme sağlar.

Media Services bir hesap oluşturduğunuzda, bir durdurulmuş durumda sizin için **varsayılan** bir akış uç noktası oluşturulur. **Varsayılan** akış uç noktasını silemezsiniz. Hesap altında ek akış uç noktaları oluşturulabilir (bkz. [Kotalar ve sınırlamalar](limits-quotas-constraints.md)). 

> [!NOTE]
> Video akışını başlatmak için videoyu akışını istediğiniz **akış uç noktasını** başlatmanız gerekir. 
>  
> Yalnızca akış uç noktanız çalışır durumdaysa faturalandırılırsınız.

## <a name="naming-convention"></a>Adlandırma kuralı

Akış URL 'sinin ana bilgisayar adı biçimi: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, burada `servicename` = akış uç noktası adı veya canlı olay adı. 

Varsayılan akış uç noktası kullanılırken, URL: `{accountname}-{regionname}.streaming.azure.net` olacak şekilde `servicename` atlanır. 

### <a name="limitations"></a>Sınırlamalar

* Akış uç noktası adı, en fazla 24 karakter uzunluğunda bir değer içerir.
* Ad şu [Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) düzenine uymalıdır: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Türler  

İki **Akış Uç Noktası** türü vardır: **Standart** (Önizleme) ve **Premium**. Tür, akış uç noktası için ayırdığınız ölçek birimi sayısıyla (`scaleUnits`) tanımlanır. 

Türler aşağıdaki tabloda açıklanmıştır:  

|Type|Ölçek birimleri|Açıklama|
|--------|--------|--------|  
|**Standart**|0|Varsayılan akış uç noktası **Standart** bir türdür, ayarlayarak `scaleUnits`Premium türüne değiştirilebilir.|
|**Premium**|>0|**Premium** Akış uç noktaları, özel ve ölçeklenebilir bant genişliği kapasitesi sağlayan gelişmiş iş yükleri için uygundur. Bir **Premium** türüne (akış birimleri) ayarlayarak `scaleUnits` geçiş yapabilirsiniz. `scaleUnits`200 Mbps 'lik artışlarla satın alınabilecek özel çıkış kapasitesi sağlar. **Premium** türü kullandığınızda etkinleştirilen her birim, uygulamaya ek bant genişliği kapasitesi sağlar. |

> [!NOTE]
> Büyük internet kitlelerine içerik teslim etmek isteyen müşteriler için, akış uç noktasında CDN 'yi etkinleştirmenizi öneririz.

SLA bilgileri için bkz. [fiyatlandırma ve SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Akış türlerini karşılaştırma

Özellik|Standart|Premium
---|---|---
Aktarım hızı |600 Mbps 'e kadar, bir CDN kullanıldığında çok daha yüksek bir verimlilik sağlar.|akış birimi başına 200 Mbps (SU). , Bir CDN kullanıldığında daha yüksek etkili bir verimlilik sağlayabilir.
CDN|Azure CDN, üçüncü taraf CDN veya CDN yok.|Azure CDN, üçüncü taraf CDN veya CDN yok.
Faturalandırma eşit olarak dağıtılır| Günlük|Günlük
Dinamik şifreleme|Evet|Evet
Dinamik paketleme|Evet|Evet
Ölçek|Hedeflenen işleme kadar otomatik olarak ölçeklendirin.|Ek SUs
IP filtreleme/G20/özel ana bilgisayar <sup>1</sup>|Evet|Evet
Aşamalı indirme|Evet|Evet
Önerilen kullanım |Akış senaryolarının çoğunluğu için önerilir.|Profesyonel kullanım.

<sup>1</sup> yalnızca CDN bitiş noktasında etkin olmadığında doğrudan akış uç noktasında kullanılır.<br/>

## <a name="properties"></a>properties 

Bu bölüm bazı akış uç noktasının özellikleriyle ilgili ayrıntıları sağlar. Yeni bir akış uç noktası ve tüm özelliklerin açıklamalarını oluşturma örnekleri için bkz. [akış uç noktası](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl`-Bu akış uç noktası için aşağıdaki güvenlik ayarlarını yapılandırmak üzere kullanılır: Akamai imza üst bilgisi kimlik doğrulama anahtarları ve bu uç noktaya bağlanmasına izin verilen IP adresleri.<br />Bu özellik yalnızca false olarak ayarlandığında ayarlanabilir `cdnEnabled` .
- `cdnEnabled`-Bu akış uç noktası için Azure CDN tümleştirme özelliğinin etkinleştirilip etkinleştirilmeyeceğini belirtir (varsayılan olarak devre dışı). True olarak ayarlarsanız `cdnEnabled` aşağıdaki konfigürasyonlar devre dışı bırakılır: `customHostNames` ve `accessControl`.
  
    Tüm veri merkezleri Azure CDN tümleştirmeyi desteklemez. Veri merkezinizde Azure CDN tümleştirmesinin mevcut olup olmadığını denetlemek için aşağıdakileri yapın:
 
  - Öğesini true `cdnEnabled` olarak ayarlamayı deneyin.
  - CDN özelliği geçerli bölgede kullanılamadığından, `HTTP Error Code 412` "akış uç noktası cdnenabled özelliği true olarak ayarlanamıyor" iletisiyle döndürülen sonucu (preconditionfailed) denetleyin. " 

    Bu hatayı alırsanız veri merkezi onu desteklemez. Başka bir veri merkezini denemeniz gerekir.
- `cdnProfile`-True olarak ayarlandığında değerleri de geçirebilirsiniz `cdnProfile`. `cdnEnabled` `cdnProfile`CDN uç nokta noktasının oluşturulacağı CDN profilinin adıdır. Var olan bir cdnProfile sağlayabilirsiniz veya yenisini kullanabilirsiniz. Değer null ise ve `cdnEnabled` true ise, "AzureMediaStreamingPlatformCdnProfile" varsayılan değeri kullanılır. Belirtilen `cdnProfile` zaten varsa, altında bir uç nokta oluşturulur. Profil yoksa, yeni bir profil otomatik olarak oluşturulur.
- `cdnProvider`-CDN etkinleştirildiğinde değerleri de geçirebilirsiniz `cdnProvider` . `cdnProvider`hangi sağlayıcının kullanılacağını denetler. Şu anda üç değer desteklenir: "StandardVerizon", "PremiumVerizon" ve "Standardadkamai". Değer sağlanmazsa ve `cdnEnabled` true ise, "StandardVerizon" kullanılır (varsayılan değer).
- `crossSiteAccessPolicies`-Çeşitli istemciler için çapraz site erişim ilkeleri belirtmek için kullanılır. Daha fazla bilgi için bkz. [etki alanları arası ilke dosyası belirtimi](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) ve [bir hizmetin etki alanı sınırları genelinde kullanılabilir hale getirilmesi](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>Ayarlar yalnızca Kesintisiz Akış için geçerlidir.
- `customHostNames`-Özel bir ana bilgisayar adına yönlendirilmiş trafiği kabul etmek üzere bir akış uç noktası yapılandırmak için kullanılır.  Bu özellik standart ve Premium akış uç noktaları için geçerlidir ve şu durumlarda `cdnEnabled`ayarlanabilir: false.
    
    Etki alanı adının sahipliğinin Media Services tarafından onaylanması gerekir. Media Services, kullanımda olan etki alanına eklenecek bir bileşen `CName` olarak Media Services hesap kimliğini içeren bir kayıt isteyerek etki alanı adı sahipliğini doğrular. Örnek olarak, "Sports.contoso.com" için, akış uç noktası için özel bir ana bilgisayar adı olarak kullanılmak üzere, için `<accountId>.contoso.com` bir kayıt Media Services doğrulama ana bilgisayar adlarından birine işaret etmek üzere yapılandırılmalıdır. Doğrulama ana bilgisayar adı verifydns ' den oluşur. \<mediaservices-DNS-Zone >. 

    Aşağıda, farklı Azure bölgeleri için doğrulama kaydında kullanılacak beklenen DNS bölgeleri verilmiştir.
  
  - Kuzey Amerika, Avrupa, Singapur, Hong Kong ÖİB, Japonya:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - Arasında
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Örneğin, "945a4c4e-28ea-45CD-8ccb-a519f6b700ad.contoso.com `CName` " ile "verifydns.Media.Azure.net" eşleyen bir kayıt, Media Services ID 945a4c4e-28ea-45CD-8ccb-a519f6b700ad 'nin contoso.com etki alanının sahipliğinin sahip olduğunu kanıtlar, bu nedenle contoso.com altında herhangi bir ad, bu hesap altındaki bir akış uç noktası için özel ana bilgisayar adı olarak kullanılabilir. Medya hizmeti KIMLIK değerini bulmak için [Azure Portal](https://portal.azure.com/) gidin ve medya hizmeti hesabınızı seçin. **Hesap kimliği** sayfanın sağ üst kısmında görünür.
        
    `CName` Kayıt için uygun bir doğrulama yapılmadan özel ana bilgisayar adı ayarlamaya yönelik bir girişim varsa, DNS yanıtı başarısız olur ve sonra bir süre önbelleğe alınır. Uygun bir kayıt olduktan sonra, önbelleğe alınmış yanıt yeniden doğrulandıktan kadar biraz zaman alabilir. Özel etki alanı için DNS sağlayıcısına bağlı olarak, kaydın yeniden doğrulanması birkaç dakika ile bir saate kadar sürebilir.
        
    `CName` İle `<accountId>.<parent domain>` `sports.contoso.com` `CName` eşlendiği öğesine ek olarak, özel ana bilgisayar adını (örneğin,) Media Services akış uç noktasının ana bilgisayar adına eşleyen bir tane oluşturmanız gerekir (örneğin, `verifydns.<mediaservices-dns-zone>` `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Aynı veri merkezinde bulunan akış uç noktaları, aynı özel ana bilgisayar adını paylaşamaz.

    Şu anda, Media Services özel etki alanlarıyla SSL 'yi desteklemez. 
    
- `maxCacheAge`-Medya parçalarında ve isteğe bağlı bildirimlerde akış uç noktası tarafından ayarlanan varsayılan en yüksek yaş HTTP önbelleği denetim üstbilgisini geçersiz kılar. Değer saniye cinsinden ayarlanır.
- `resourceState` -

    - Durduruldu-bir akış uç noktasının oluşturulduktan sonra başlangıçtaki durumu
    - Başlatılıyor-çalışma durumuna geçiyor
    - Çalışıyor-istemcilere içerik akışını sağlayabiliyor
    - Ölçeklendirme-ölçek birimleri artırıldı veya azaltılır
    - Durduruluyor-durdurulmuş duruma geçiyor
    - Silme-siliniyor
    
- `scaleUnits`-Size 200 Mbps 'lik artışlarla satın alınabilecek özel çıkış kapasitesi sağlar. **Premium** bir türe taşımanız gerekiyorsa, öğesini ayarlayın `scaleUnits`.

## <a name="working-with-cdn"></a>CDN ile çalışma

Çoğu durumda CDN'yi etkinleştirmeniz gerekir. Ancak eşzamanlı görüntüleyici sayısının 500'ün altında olmasını bekliyorsanız CDN en iyi ölçeklendirme performansını eşzamanlılık ile birlikte sunduğundan CDN'yi devre dışı bırakmanız önerilir.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Akış uç noktası `hostname` ve akış URL 'si, CDN 'yi etkinleştirip etkinleştirmeksizin aynı kalır.
* İçeriğinizi CDN ile veya CDN olmadan test etmek istiyorsanız, CDN etkin olmayan başka bir akış uç noktası oluşturabilirsiniz.

### <a name="detailed-explanation-of-how-caching-works"></a>Önbelleğe almanın nasıl çalıştığına ilişkin ayrıntılı açıklama

CDN 'nin etkinleştirildiği bir akış uç noktası için gereken bant genişliği miktarı değiştiğinden CDN eklenirken belirli bir bant genişliği değeri yok. Bir lot, içerik türüne, ne kadar popüler olduğuna, bitme ve protokollerden bağlıdır. CDN yalnızca istenmekte olan işlemleri önbelleğe alırken. Diğer bir deyişle, video parçası önbelleğe alındığı sürece popüler içeriğin CDN 'den doğrudan sunulması anlamına gelir. Tam olarak aynı şeyi izleyen çok sayıda kişi olduğu için canlı içeriğin önbelleğe alınması olasıdır. Çok daha popüler ve bazıları olmayan bazı içeriklere sahip olabileceğinden isteğe bağlı içerik biraz karmaşık olabilir. Bunlardan hiçbirinin popüler (yalnızca 1 veya 2 Görüntüleyici), ancak tüm farklı videoları izleyen binlerce kişiye sahipseniz, CDN çok daha az etkin hale gelir. Bu önbellek isabetsizliği ile akış uç noktasındaki yükü artırabilirsiniz.
 
Ayrıca, uyarlamalı akışın nasıl çalıştığını göz önünde bulundurmanız gerekir. Her tek video parçası kendi varlığı olarak önbelleğe alınır. Örneğin, belirli bir video ilk kez izlenirse, kişi burada yalnızca birkaç saniye daha izlemeyi atlar ve yalnızca CDN 'de ne kadar izlenen kişilerin yaptığı ile ilişkili video parçaları vardır. Uyarlamalı akış sayesinde genellikle 5 ile 7 arasında farklı bit fiyatları vardır. Bir kişi bir bit hızı izlerken ve başka bir kişi farklı bir bit hızı izlerse, her biri CDN 'de ayrı olarak önbelleğe alınır. İki kişi aynı bit hızını izliyor olsa da, farklı protokoller üzerinden akış olabilir. Her protokol (HLS, MPEG-DASH, Kesintisiz Akış) ayrı olarak önbelleğe alınır. Böylece her bit hızı ve protokol ayrı olarak önbelleğe alınır ve yalnızca istenen video parçaları önbelleğe alınır.

### <a name="enable-azure-cdn-integration"></a>Azure CDN tümleştirmeyi etkinleştir

CDN etkinken bir akış uç noktası sağlandıktan sonra, akış uç noktasını CDN uç noktasına eşlemek için DNS güncelleştirme işlemi yapılmadan önce Media Services tanımlı bir bekleme süresi vardır.

Daha sonra CDN 'yi devre dışı bırakmak/etkinleştirmek istiyorsanız, akış uç noktanızın **durdurulmuş** durumda olması gerekir. Azure CDN tümleştirmenin etkinleştirilmesi ve değişikliklerin tüm CDN pop 'larda etkin olması iki saate kadar sürebilir. Ancak, akış uç noktasından kesintiler olmadan akış uç noktanızı ve akışınızı başlatabilir ve tümleştirme tamamlandıktan sonra akış CDN 'den dağıtılır. Sağlama süresi boyunca, akış uç noktanız **Başlangıç** durumunda olur ve performans düşüklüğü gözlemleyebilirsiniz.

Standart akış uç noktası oluşturulduğunda, standart Verizon ile varsayılan olarak yapılandırılır. REST API 'Lerini kullanarak Premium Verizon veya standart Akamai sağlayıcılarını yapılandırabilirsiniz. 

CDN tümleştirmesi, Çin ve Federal Kamu bölgeleri dışındaki tüm Azure veri merkezlerinde etkin olur.

> [!IMPORTANT]
> Azure CDN ile tümleştirme Azure Media Services standart akış uç noktaları için **Verizon 'tan Azure CDN** uygulanır. Premium akış uç noktaları, tüm **Azure CDN fiyatlandırma katmanları ve sağlayıcıları**kullanılarak yapılandırılabilir. Azure CDN özellikler hakkında daha fazla bilgi için bkz. [CDN 'ye genel bakış](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>DNS değişikliğinin yapıldığını belirleme

Kullanarak https://www.digwebinterface.com bir akış uç noktasında (trafiğin Azure CDN YÖNLENDIRILMEKTE) DNS değişikliği yapıldığını belirleyebilirsiniz. Sonuçlarda azureedge.net etki alanı adları varsa, trafik artık CDN 'ye işaret ediyor.

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

[Bu depodaki](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) örnek, .NET ile varsayılan akış uç noktasının nasıl başlatılacağını gösterir.


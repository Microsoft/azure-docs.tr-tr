---
title: Azure CDN'yi CORS ile kullanma | Microsoft Docs
description: Çıkış noktaları arası kaynak paylaşımı (CORS) ile Azure Content Delivery Network (CDN) için kullanmayı öğrenin.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 204183fa25203a094eecd8df85a8bfd5dcf271cc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593964"
---
# <a name="using-azure-cdn-with-cors"></a>Azure CDN'yi CORS ile kullanma
## <a name="what-is-cors"></a>CORS nedir?
CORS (çapraz kaynak kaynak paylaşımı) başka bir etki alanındaki kaynaklara erişmek bir etki alanı altında çalışan bir web uygulamasını etkinleştiren bir HTTP özelliğidir. Siteler arası betik saldırıları olasılığını azaltmak için tüm modern web tarayıcıları olarak bilinen bir güvenlik kısıtlaması uygulamak [aynı çıkış noktası İlkesi](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Bu, bir web sayfasından farklı bir etki alanında arama API'leri engeller.  CORS, başka bir kaynak API'leri çağırmak bir kaynak (kaynak etki alanı) izin vermek için güvenli bir yol sağlar.

## <a name="how-it-works"></a>Nasıl çalışır?
CORS istekleri, iki tür vardır *basit istekler* ve *karmaşık istekler.*

### <a name="for-simple-requests"></a>Basit istekleri için:

1. Tarayıcı ek bir CORS isteği gönderen **kaynak** HTTP isteği üstbilgisi. Bileşimi tanımlanır ana sayfada sunulandan kaynak bu üst bilgi değeri *Protokolü* *etki* ve *bağlantı noktası.*  Bir sayfa zaman https'den\:/ / www.contoso.com çalışır fabrikam.com kaynağı kullanıcının verilere erişmek, aşağıdaki istek üstbilgisi fabrikam.com olarak gönderilecek:

   `Origin: https://www.contoso.com`

2. Sunucu aşağıdakilerden herhangi biri ile yanıt verebilir:

   * Bir **Access-Control-Allow-Origin** hangi kaynak site izin belirten kendi yanıt üst bilgisi. Örneğin:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Sunucu çıkış noktaları arası istek kaynak üst bilgisi denetledikten sonra izin vermiyorsa 403 gibi HTTP hata kodu

   * Bir **Access-Control-Allow-Origin** tüm kaynaklara izin veren bir joker karakter üstbilgiyle:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Karmaşık istekleri için:

Karmaşık bir CORS isteği göndermek için tarayıcı burada gerekli isteğidir bir *denetim öncesi isteği* (diğer bir deyişle, ilk araştırma) fiili CORS isteğini göndermeden önce. Denetim öncesi isteği özgün CORS devam edebilir ve olduğundan isteği sunucu izni ister bir `OPTIONS` aynı URL'sine istek.

> [!TIP]
> CORS akışlar ve yaygın görülen tehlikeleri hakkında daha fazla ayrıntı için görüntüleme [REST API'leri için CORS Kılavuzu](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Joker karakter veya tek bir kaynak senaryoları
Azure cdn'de CORS ek bir yapılandırma olmadan otomatik olarak çalışır, **Access-Control-Allow-Origin** joker karakter (*) veya tek bir kaynak için üst bilgisini ayarlayın.  CDN ilk yanıtı önbelleğe alır ve sonraki istekleri aynı üst bilgiyi kullanır.

İstek zaten CDN'yi CORS kaynağınızda ayarlamadan önce yapıldı, içerikle yeniden yüklemek için uç nokta içeriğinizle içeriği temizlemek gerekir **Access-Control-Allow-Origin** başlığı.

## <a name="multiple-origin-scenarios"></a>Birden çok kaynak senaryoları
Belirli bir CORS için izin verilecek çıkış noktaları listesi izin vermeniz gerekiyorsa, biraz daha karmaşık işlerinizi. CDN'nin önbelleğe aldığını sorun oluşur **Access-Control-Allow-Origin** ilk CORS kaynağı başlığı.  Farklı bir CORS kaynağı bir sonraki istekte bulunduğunda CDN önbelleğe alınan hizmet **Access-Control-Allow-Origin** eşleşmeyecektir üst bilgisi.  Bu sorunu gidermek için birkaç yolu vardır.

### <a name="azure-cdn-premium-from-verizon"></a>Verizon’dan Azure CDN Premium
Bunu etkinleştirmek için en iyi yolu kullanmaktır **verizon'dan Azure CDN Premium**, gelişmiş işlevsellik bazı sunar. 

Şunları yapmanız gerekir [kural oluşturma](cdn-rules-engine.md) denetlemek için **kaynak** isteği üstbilgisi.  Geçerli bir kaynak varsa, kural ayarlayacak **Access-Control-Allow-Origin** istekte sağlanan kaynak üstbilgiyle.  Kaynak belirtilmişse **kaynak** üst bilgi verilmez, kural atlamak **Access-Control-Allow-Origin** başlığı, bu isteği reddetmek tarayıcıya çalışmamasına neden olur. 

Kural altyapısı ile bunu yapmanın iki yolu vardır. Her iki durumda da **Access-Control-Allow-Origin** üstbilgi dosyasının kaynak sunucudan göz ardı edilir ve CDN'in kurallar altyapısı tamamen izin verilen CORS çıkış noktaları yönetir.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Tüm geçerli kaynaklara sahip bir normal ifade
Bu durumda, tüm izin vermek istediğiniz kaynakları içeren bir normal ifade oluşturacaksınız: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Verizon'dan Azure CDN Premium** kullanan [Perl uyumlu normal ifadeler](https://pcre.org/) normal ifadeler için kendi altyapısı olarak.  Gibi bir araç kullanabilirsiniz [normal ifadeler 101](https://regex101.com/) normal ifadeniz doğrulamak için.  "/" Karakterini normal ifadelerde geçerli olduğundan ve kaçış karakterleri gerekmez, ancak bu karakter atlanıyor ve en iyi uygulama olarak kabul edilir bazı regex doğrulayıcıları tarafından beklenen dikkat edin.
> 
> 

Normal ifadeyle eşleşen, kural yerini alacak **Access-Control-Allow-Origin** isteğin kaynağına sahip kaynaktan üstbilgi (varsa).  Ek CORS üst bilgileri gibi ekleyebilirsiniz **erişim-denetim-Allow-Methods**.

![Normal ifade ile kuralları örneği](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Her kaynak için istek üst bilgisi kuralı.
Normal ifadeler yerine, bunun yerine kullanarak izin vermek istediğiniz her kaynak için ayrı bir kural oluşturabilirsiniz **istek üst bilgisi joker** [eşleşen koşul](/previous-versions/azure/mt757336(v=azure.100)#match-conditions). Normal ifade yöntemiyle olduğu gibi tek başına kural altyapısı CORS üstbilgilerini ayarlar. 

![Normal ifade olmadan kuralları örneği](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> Joker karakter kullanmanın yukarıdaki örnekte * hem HTTP hem de HTTPS eşleştirmek için kurallar altyapısı söyler.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Azure CDN standart profilleri
Azure CDN standart profilleri (**Azure CDN standart Microsoft gelen**, **akamai'den Azure CDN standart**, ve **verizon'dan Azure CDN standart**), yalnızca mekanizması joker karakter kaynak kullanımını kullanmaktır olmadan için birden çok kaynaklara izin [sorgu dizesini önbelleğe alma](cdn-query-string.md). CDN uç noktası için sorgu dizesi ayarını etkinleştirin ve ardından izin verilen her etki alanı gelen istekleri için bir benzersiz sorgu dizesini kullanın. Bunun yapılması, ayrı bir nesne için her bir benzersiz sorgu dizesini önbelleğe alma bir CDN neden olur. Bu yaklaşım ancak ideal, değil olarak CDN'de önbelleğe alınmış aynı dosyanın birden çok kopyasını neden.  


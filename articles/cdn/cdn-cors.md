---
title: CORS ile Azure CDN kullanma | Microsoft Docs
description: Azure Content Delivery Network 'yi (CDN), çıkış noktaları arası kaynak paylaşımı (CORS) ile birlikte nasıl kullanacağınızı öğrenin.
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
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f7edf790e526329dd285d03a31137a26220e52ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018656"
---
# <a name="using-azure-cdn-with-cors"></a>CORS ile Azure CDN kullanma
## <a name="what-is-cors"></a>CORS nedir?
CORS (Çıkış Noktaları Arası Kaynak Paylaşımı), bir etki alanında çalışan web uygulamasının başka bir etki alanındaki kaynaklara erişmesini sağlayan bir HTTP özelliğidir. Siteler arası komut dosyası saldırıları olasılığını azaltmak için, tüm modern web tarayıcıları [aynı kaynak ilkesi](https://www.w3.org/Security/wiki/Same_Origin_Policy)olarak bilinen bir güvenlik kısıtlaması uygular.  Bu, bir Web sayfasının farklı bir etki alanındaki API 'Leri aramasını engeller.  CORS, farklı bir kaynaktan API 'Leri çağırmak için bir kaynağa (kaynak etki alanı) izin vermek için güvenli bir yol sağlar.

## <a name="how-it-works"></a>Nasıl çalışır?
İki tür CORS isteği, *basit istekler* ve *karmaşık istekler vardır.*

### <a name="for-simple-requests"></a>Basit istekler için:

1. Tarayıcı, CORS isteğini ek bir **kaynak** http istek üstbilgisiyle gönderir. Bu üstbilginin değeri, üst sayfayı sunan, *protokol,* *etki alanı* ve *bağlantı noktası* birleşimi olarak tanımlanan başlangıç noktasıdır.  Https//www.contoso.com 'deki bir sayfa, \: fabrikam.com kaynağından bir kullanıcının verilerine erişmeyi denediğinde, aşağıdaki istek üst bilgisi fabrikam.com 'e gönderilir:

   `Origin: https://www.contoso.com`

2. Sunucu, aşağıdakilerden biriyle yanıt verebilir:

   * Yanıtında bir **erişim-denetim-Izin verme-kaynak** üst bilgisi, hangi kaynak siteye izin verildiğini belirtir. Örnek:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Sunucu, kaynak üstbilgisini denetledikten sonra çıkış noktaları arası isteğe izin vermezse 403 gibi bir HTTP hata kodu

   * Tüm kaynakları izin veren bir joker karakter içeren bir **erişim-denetim-izin-kaynak** üst bilgisi:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Karmaşık istekler için:

Karmaşık bir istek, tarayıcının gerçek CORS isteğini göndermeden önce bir *ön kontrol isteği* (yani bir ön araştırma) göndermesi için gereken bir CORS isteğidir. Özgün CORS isteği devam edebiliyorsa ve aynı URL 'ye bir istek ise, ön kontrol isteği sunucu iznini ister `OPTIONS` .

> [!TIP]
> CORS akışları ve genel bilgiler hakkında daha fazla bilgi için, [REST API 'leri IÇIN CORS Kılavuzu](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)' na bakın.
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Joker karakter veya tek kaynak senaryoları
**Erişim-denetim-izin-kaynak** üstbilgisi joker (*) veya tek bir Origin olarak AYARLANDıĞıNDA Azure CDN CORS, ek yapılandırma olmadan otomatik olarak çalışacaktır.  CDN ilk yanıtı önbelleğe alacak ve sonraki istekler aynı üstbilgiyi kullanacaktır.

Kaynak üzerinde CORS 'den önce CDN 'de istekler zaten yapılmışsa, içerik **erişim-denetim-izin-kaynak** üstbilgisiyle yeniden yüklemek için uç nokta içeriklerindeki içeriği temizlemeniz gerekir.

## <a name="multiple-origin-scenarios"></a>Birden çok kaynak senaryosu
CORS için belirli bir çıkış listesine izin verilmesi gerekiyorsa, bu şeyler biraz daha karmaşıktır. Bu sorun, CDN ilk CORS kaynağı için **Access-Control-Allow-Origin** üst bilgisini önbelleğe alırken oluşur.  Farklı bir CORS kaynağı sonraki bir istek yaptığında CDN, önbelleğe alınan **Access-Control-Allow-Origin** üst bilgisine sahip olur ve bu eşleşmez.  Bunu düzeltmek için birkaç yol vardır.

### <a name="azure-cdn-standard-profiles"></a>Standart profiller Azure CDN
Microsoft 'tan Azure CDN Standard 'da, istekteki **kaynak** üst bilgisini denetlemek için [standart kurallar altyapısında](cdn-standard-rules-engine-reference.md) bir kural oluşturabilirsiniz. Geçerli bir Origin ise, kuralınız **erişim-Control-Allow-Origin** üst bilgisini istenen değere ayarlar. Bu durumda, dosyanın kaynak sunucusundan **erişim-denetim-izin-kaynak** üst bilgisi yok SAYıLıR ve CDN 'nin Rules altyapısı ızın verilen CORS kaynakları 'nı tamamen yönetir.

![Standart kurallar altyapısına sahip kurallar örneği](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> **Erişim-denetimi-Izin verme yöntemleri** gibi ek yanıt üstbilgilerini değiştirmek için kuralınıza ek eylemler ekleyebilirsiniz.
> 

**Akamai ' dan Azure CDN Standart**, joker karakter kaynağını kullanmadan birden fazla kaynağa izin veren tek mekanizma [sorgu dizesi önbelleğe almayı](cdn-query-string.md)kullanmaktır. CDN uç noktası için sorgu dizesi ayarını etkinleştirin ve ardından izin verilen her etki alanından gelen istekler için benzersiz bir sorgu dizesi kullanın. Bunun yapılması, benzersiz bir sorgu dizesi için CDN önbelleğine ayrı bir nesne olarak neden olur. Ancak bu yaklaşım ideal değildir, ancak CDN 'de önbelleğe alınan aynı dosyanın birden çok kopyasının oluşmasına neden olur.  

### <a name="azure-cdn-premium-from-verizon"></a>Verizon 'ten Premium Azure CDN
Verizon Premium kuralları altyapısını kullanarak, istekteki **kaynak** üst bilgisini denetlemek için [bir kural oluşturmanız](./cdn-verizon-premium-rules-engine.md) gerekir.  Geçerli bir başlangıç noktası ise, kuralınız **erişim-Control-Allow-Origin** üst bilgisini istekte verilen kaynağa ayarlar.  **Kaynak** üstbilgisinde belirtilen kaynağa izin verilmiyorsa, kuralınız, tarayıcının isteği reddetmesine neden olacak şekilde **erişim-denetim-izin-Origin** üst bilgisini atmalıdır. 

Bunu Premium kurallar altyapısı ile gerçekleştirmenin iki yolu vardır. Her iki durumda da, dosyanın kaynak sunucusundaki **erişim-denetim-izin-kaynak** üst bilgisi yok SAYıLıR ve CDN 'nin Rules altyapısı ızın verilen CORS kaynakları 'nı tamamen yönetir.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Tüm geçerli kaynakları içeren bir normal ifade
Bu durumda, izin vermek istediğiniz tüm kaynakları içeren bir normal ifade oluşturacaksınız: 

```http
https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
```

> [!TIP]
> **Verizon ' dan Premium Azure CDN** , normal ifadeler için kendi altyapısı olarak [Perl uyumlu normal ifadelerini](https://pcre.org/) kullanır.  Normal ifadenizi doğrulamak için [normal ifadeler 101](https://regex101.com/) gibi bir araç kullanabilirsiniz.  "/" Karakterinin normal ifadelerde geçerli olduğunu ve kaçmasına gerek duymadığını, ancak bu karakterin kaçış için en iyi uygulama olarak kabul edileceğini ve bazı Regex doğrulayıcıları tarafından beklendiğini unutmayın.
> 
> 

Normal ifade eşleşiyorsa kuralınız, **erişim-denetimi-izin-kaynak** üst bilgisini (varsa), isteği gönderen kaynağa göre değiştirir.  Ayrıca, **Access-Control-Allow-Methods** gıbı ek CORS üst bilgileri de ekleyebilirsiniz.

![Normal ifadeyle kurallar örneği](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Her kaynak için istek üst bilgisi kuralı.
Normal ifadeler yerine, **Istek üst bilgisi** [eşleştirme koşulunun](/previous-versions/azure/mt757336(v=azure.100)#match-conditions)kullanılmasına izin vermek istediğiniz her kaynak için ayrı bir kural oluşturabilirsiniz. Normal ifade yönteminde olduğu gibi, kural altyapısı yalnızca CORS üst bilgilerini ayarlar. 

![Normal ifade olmayan kurallar örneği](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> Yukarıdaki örnekte joker karakter * kullanımı, Rules altyapısına hem HTTP hem de HTTPS ile eşleşmesini söyler.
> 
>
---
title: Önbelleğe alma nasıl çalışır | Microsoft Docs
description: Önbelleğe alma, böylece verileri daha hızlı bir şekilde erişilebilir için gelecek istekleri, verileri yerel olarak depolamak işlemidir.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: magattus
ms.openlocfilehash: 92d93fbf9fa2f8df15acb62802d7ac53db836dc1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593853"
---
# <a name="how-caching-works"></a>Önbelleğe alma nasıl işler?

Bu makalede genel önbelleğe alma kavramlarına genel bir bakış sağlar ve nasıl [Azure Content Delivery Network (CDN)](cdn-overview.md) performansını artırmak için önbelleğe alma kullanır. CDN uç noktanız üzerinde önbelleğe alma davranışını özelleştirmek bkz hakkında bilgi almak istiyorsanız, [denetimi Azure CDN önbelleğe alma kuralları ile önbelleğe alma davranışını](cdn-caching-rules.md) ve [denetimi Azure CDN önbelleğe alma davranışını sorgu dizeleriyle](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Önbelleğe alma giriş

Önbelleğe alma, böylece verileri daha hızlı bir şekilde erişilebilir için gelecek istekleri, verileri yerel olarak depolamak işlemidir. Önbelleğe alma, web tarayıcısı önbelleğe alma, bir web en yaygın türü tarayıcı statik verilerin kopyasını yerel bir sabit sürücüde yerel olarak depolar. Web tarayıcısı, önbelleğe alma kullanarak sunucuya birden çok gidiş dönüş yapmaktan kaçının ve bunun yerine zaman ve kaynak vermeyip aynı verilere yerel olarak erişim. Önbelleğe alma, yerel olarak statik görüntüler, CSS dosyaları ve JavaScript dosyaları gibi küçük, statik verileri yönetmek için çok uygundur.

Benzer şekilde, önbelleğe alma uç sunucularda kullanıcı yakın content delivery network kaynağa geri seyahat ve son kullanıcı gecikme süresini azaltma istekleri önlemek için kullanılır. Yalnızca tek bir kullanıcı için kullanılan bir web tarayıcısının önbelleğine, paylaşılan bir önbellek CDN sahiptir. CDN paylaşılan Önbellek içinde bir kullanıcı tarafından istenen bir dosya daha sonra diğer kullanıcılar tarafından hangi kaynak sunucu isteklerinin sayısını önemli ölçüde azaltır erişilebilir.

Sık sık değişen veya bireysel bir kullanıcı için benzersiz olan dinamik kaynaklar önbelleğe alınamaz. Bu türdeki kaynağı ancak dinamik site Hızlandırma (DSA) en iyi duruma getirme performans geliştirmeleri için Azure Content Delivery Network üzerindeki yararlanabilirsiniz.

Önbelleğe alma, kaynak sunucu ve son kullanıcı arasında birden fazla düzeyde oluşabilir:

- Web sunucusu: Paylaşılan önbellek (birden çok kullanıcı için) kullanır.
- İçerik teslim ağı: Paylaşılan önbellek (birden çok kullanıcı için) kullanır.
- Internet servis sağlayıcınıza (ISS): Paylaşılan önbellek (birden çok kullanıcı için) kullanır.
- Web tarayıcı: Özel bir önbellek (bir kullanıcı için) kullanır.

Her önbellek genellikle kendi kaynak yeniliği yönetir ve bir dosya eski olduğunda doğrulama gerçekleştirir. Bu davranışı HTTP belirtimine, önbelleğe alma tanımlanır [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Kaynak yeniliği

Önbelleğe alınmış kaynak potansiyel olarak güncel olmayan ya da eski (karşılaştırıldığında, kaynak sunucuda karşılık gelen kaynak). Bu nedenle, içerik yenilendiğinde tüm önbelleğe alma mekanizması denetlemek için önemlidir. Her erişildiğinde süreyi ve bant genişliği tüketimi kaydetmek için önbelleğe alınmış kaynak kaynak sunucu sürümünde karşılaştırılır değil. Bunun yerine, önbelleğe alınmış kaynak yeni olarak kabul edilir sürece, en güncel sürümü olarak kabul edilir ve doğrudan istemciye gönderilir. Önbelleğe alınmış kaynak, yaş yaş veya bir önbellek ayarı tarafından tanımlanan süre değerinden olduğunda yeni olarak kabul edilir. Örneğin, bir tarayıcı bir web sayfası yüklediğinde, önbelleğe alınan her kaynak sabit sürücünüzdeki yeni ve yükler doğrular. Kaynak yeni değilse (eski) bir güncel kopya sunucudan da yüklenir.

### <a name="validation"></a>Doğrulama

Bir kaynak eski olarak kabul edilir, kaynak sunucu istenir, yani doğrulayın, önbellekteki verilerin hala kaynak sunucuda nedir eşleşip eşleşmediğini belirlemek için. Kaynak sunucuda dosya değiştirilmiş, önbellek, kaynağın sürümünü güncelleştirir. Aksi takdirde, yeni bir kaynaktır, veriler ilk önce doğrulamadan doğrudan önbellekten gönderilir.

### <a name="cdn-caching"></a>CDN önbelleğe alma

Önbelleğe alma bir CDN teslimini hızlandırın ve görüntü, yazı tipleri ve video gibi statik varlıklar için kaynak yükü azaltmak için çalıştığı şekilde tümleşiktir. CDN önbelleğe alma, statik kaynakları seçerek bir kullanıcı için daha fazla yerel stratejik olarak yerleştirilmiş sunucularda depolanır ve aşağıdaki avantajları sağlar:

- Çoğu web trafiği (örneğin, görüntüleri, yazı tipleri ve videoları) statik olduğundan, CDN önbelleğe alma taşıyarak ağ gecikmesini içerik yakın böylece veri geçen uzaklığı azaltır kullanıcıya azaltır.

- İş için bir CDN boşaltarak, önbelleğe alma ağ trafiği ve kaynak sunucu üzerindeki yükü azaltabilir. Bunun yapılması, çok sayıda kullanıcıyı olduğunda bile, uygulama için maliyet ve kaynak gereksinimlerini azaltır.

Önbelleğe alma nasıl uygulandığını benzeyen bir web tarayıcısında, nasıl önbelleğe alma bir CDN önbellek yönergesi üstbilgileri göndererek gerçekleştirildiğini denetleyebilirsiniz. Genellikle kaynak sunucu tarafından eklenen HTTP üst bilgilerini önbellek yönergesi üst bilgilerdir. Bu üstbilgileri çoğunu başlangıçta istemci tarayıcılarında önbelleğe yönelik olarak tasarlanmış olsa da, bunlar şimdi de CDN'ler gibi tüm ara önbellekleri tarafından kullanılır. 

İki üst bilgiler, önbellek güncellik tanımlamak için kullanılabilir: `Cache-Control` ve `Expires`. `Cache-Control` Daha fazla geçerli olduğunu ve önceliklidir `Expires`, ikisi de varsa. Ayrıca üst bilgilerini doğrulama (doğrulayıcıları olarak adlandırılır) için kullanılan iki tür vardır: `ETag` ve `Last-Modified`. `ETag` Daha fazla geçerli olduğunu ve önceliklidir `Last-Modified`, her ikisi de tanımlanır.  

## <a name="cache-directive-headers"></a>Önbellek yönergesi üstbilgileri

> [!IMPORTANT]
> Varsayılan olarak, DSA için en iyi duruma getirilmiş bir Azure CDN uç noktası önbellek yönergesi üstbilgileri yoksayar ve önbelleğe alma atlar. İçin **verizon'dan Azure CDN standart** ve **akamai'den Azure CDN standart** profillerini nasıl kullanarak bir Azure CDN uç noktası bu üstbilgileri işler ayarlayabilirsiniz [CDN önbelleğe alma kuralları](cdn-caching-rules.md)önbelleğe almayı etkinleştirmek için. İçin **verizon'dan Azure CDN Premium** profilleri yalnızca kullandığınız [kurallar altyapısı](cdn-rules-engine.md) önbelleğe almayı etkinleştirmek için.

Azure CDN önbelleğe alma süresi ve önbellek paylaşımı tanımlamak aşağıdaki HTTP önbellek yönergesi üstbilgileri destekler.

**Ön bellek denetimi:**
- HTTP 1.1 web yayımcılar kendi içerik üzerinde daha fazla denetim verir ve sınırlamaları yönelik olarak sunulan `Expires` başlığı.
- Geçersiz kılmalar `Expires` başlığı, her iki bunu ve `Cache-Control` tanımlanır.
- CDN POP istemciden bir HTTP isteği kullanıldığında `Cache-Control` varsayılan olarak tüm Azure CDN profili tarafından göz ardı edilir.
- CDN POP istemciden bir HTTP yanıtına kullanıldığında:
     - **Verizon'dan Azure CDN standart/Premium** ve **Azure CDN standart Microsoft gelen** tüm destek `Cache-Control` yönergeleri.
     - **Azure CDN standart Akamai** yalnızca şunları desteklemektedir `Cache-Control` yönergeleri; tüm diğer göz ardı edilir:
         - `max-age`: Bir önbellek içeriği için belirtilen saniye sayısı depolayabilirsiniz. Örneğin: `Cache-Control: max-age=5`. Bu yönerge, içeriği yeni olarak kabul edilir en uzun süreyi belirtir.
         - `no-cache`: İçeriği önbelleğe ancak içeriği önbellekten önce teslim etme her zaman doğrulayın. Eşdeğer `Cache-Control: max-age=0`.
         - `no-store`: Hiçbir zaman içeriği önbelleğe alın. Daha önce depolanmışsa içeriği kaldırın.

**Süre sonu:**
- HTTP 1.0 sunulan eski başlığı; için geriye dönük uyumluluk desteklenmiyor.
- Tarih temelli bir süre ile ikinci duyarlık kullanır. 
- Benzer şekilde `Cache-Control: max-age`.
- Kullanılabilir `Cache-Control` yok.

**Pragma:**
   - Azure CDN tarafından varsayılan olarak kabul değil.
   - HTTP 1.0 sunulan eski başlığı; için geriye dönük uyumluluk desteklenmiyor.
   - Aşağıdaki yönerge bir istemci istek üstbilgisi olarak kullanılan: `no-cache`. Bu yönerge, yeni bir kaynak sürümünü sunmak için sunucunun bildirir.
   - `Pragma: no-cache` eşdeğerdir `Cache-Control: no-cache`.

## <a name="validators"></a>Doğrulayıcıları

Önbellek eski olduğunda, HTTP önbellek Doğrulayıcıların bir dosyayı önbelleğe alınmış sürümünü kaynak sunucudaki sürümüyle karşılaştırmak için kullanılır. **Verizon'dan Azure CDN standart/Premium** her ikisini de destekler `ETag` ve `Last-Modified` doğrulayıcıları varsayılan olarak, ancak **Azure CDN standart Microsoft gelen** ve **akamai'denAzureCDNstandart** yalnızca destekler `Last-Modified` varsayılan olarak.

**ETag:**
- **Verizon'dan Azure CDN standart/Premium** destekler `ETag` varsayılan olarak, ancak **Azure CDN standart Microsoft** ve **akamai'den Azure CDN standart** değildir.
- `ETag` her dosya ve dosya sürümü için benzersiz bir dize tanımlar. Örneğin: `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- HTTP 1.1 içinde tanıtılan ve daha güncel `Last-Modified`. Son değişiklik tarihini saptamak zor olduğunda yararlıdır.
- Güçlü doğrulama hem zayıf doğrulama destekler. Ancak, Azure CDN, yalnızca sağlam doğrulaması destekler. Güçlü doğrulama için iki kaynak gösterimleri bayt için olmalıdır aynı. 
- Bir önbellek kullanan bir dosya doğrular `ETag` göndererek bir `If-None-Match` bir veya daha fazla üst bilgi `ETag` istekteki doğrulayıcıları. Örneğin: `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Sunucu sürümü eşleşmesi durumunda bir `ETag` yanıtına 304 (değiştirilmedi) durum kodu gönderdiği Doğrulayıcı listesinde. Sürüm farklı ise, sunucu durum kodu ile 200 (Tamam) ve güncelleştirilmiş kaynak yanıt verir.

**Son değiştiren:**
- İçin **Azure CDN standart/Premium verizon'dan** yalnızca `Last-Modified` kullanılır `ETag` HTTP yanıtını bir parçası değil. 
- Kaynağın son değiştirildiği kaynak sunucu belirledi saat ve tarihi belirtir. Örneğin: `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Bir önbellek kullanarak bir dosyayı doğrular `Last-Modified` göndererek bir `If-Modified-Since` sahip bir tarih ve saat istekteki üstbilgi. Kaynak sunucu o tarihle karşılaştırır `Last-Modified` en son kaynak üstbilgisi. Kaynağın belirtilen zamanından bu yana değiştirilmedi, sunucunun yanıt olarak 304 (değiştirilmedi) durum kodu döndürür. Kaynak değiştirilirse sunucu durumu döndürür. kod 200 (Tamam) ve güncelleştirilmiş kaynak.

## <a name="determining-which-files-can-be-cached"></a>Hangi dosyaların önbelleğe alınabilir belirleme

Tüm kaynaklar önbelleğe alınabilir. Aşağıdaki tablo, hangi kaynakların, HTTP yanıtı türüne göre önbelleğe alınabilir gösterir. Bu koşulların tümüyle sağlamayan HTTP yanıtlarını ile sunulan kaynakları önbelleğe alınamaz. İçin **verizon'dan Azure CDN Premium** yalnızca, Bu koşullardan bazıları özelleştirmek için bir kural altyapısı kullanabilirsiniz.

|                   | Microsoft Azure CDN          | Verizon'dan Azure CDN | Akamai'den Azure CDN        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| HTTP durum kodları | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| HTTP yöntemleri      | GET, HEAD                         | GET                    | GET                          |
| Dosya boyutu sınırları  | 300 GB                            | 300 GB                 | -Genel web teslimatı iyileştirmesi: 1,8 GB<br />-Medya akış iyileştirmeleri: 1,8 GB<br />-Büyük dosya iyileştirmesi: 150 GB |

İçin **Azure CDN standart Microsoft gelen** bir kaynak üzerinde çalışmak için önbelleğe alma, kaynak sunucu, tüm HEAD desteklemesi gerekir ve GET HTTP isteklerini ve içerik-uzunluk değerleri varlığın tüm baş ve GET HTTP yanıtları için aynı olması gerekir. HEAD isteği, kaynak sunucu HEAD isteği desteklemesi gerekir ve bir GET isteği almış sanki aynı üst bilgilere ile yanıt vermelidir.

## <a name="default-caching-behavior"></a>Varsayılan önbelleğe alma davranışı

Aşağıdaki tabloda, önbelleğe alma davranışını Azure CDN ürünü ve kendi en iyi duruma getirme için varsayılan açıklanmaktadır.

|    | Microsoft: Genel web teslimatı | Verizon: Genel web teslimatı | Verizon: DSA | Akamai: Genel web teslimatı | Akamai: DSA | Akamai: Büyük dosya indirme | Akamai: genel veya VOD medya akışı |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Kaynak Uy**       | Evet    | Evet   | Hayır   | Evet    | Hayır   | Evet   | Evet    |
| **CDN önbelleğe alma süresi** | 2 gün |7 gün | None | 7 gün | None | 1 gün | 1 yıl |

**Kaynak dikkate**: Kaynak sunucusundan gelen HTTP yanıtında varsa desteklenen önbellek yönergesi üstbilgileri uymanız belirtir.

**CDN önbelleğe alma süresi**: Azure CDN'de bir kaynak olarak önbelleğe süreyi belirtir. Ancak, **dikkate kaynak** Evet ve kaynak sunucusundan gelen HTTP yanıt önbellek yönergesi üst bilgisi içeren `Expires` veya `Cache-Control: max-age`, Azure CDN, bunun yerine üstbilgisi tarafından belirtilen süre değeri kullanır. 

## <a name="next-steps"></a>Sonraki adımlar

- Özelleştirme ve önbelleğe alma davranışını önbelleğe alma kuralları aracılığıyla cdn'de varsayılan geçersiz kılma hakkında bilgi edinmek için [denetimi Azure CDN önbelleğe alma kuralları ile önbelleğe alma davranışını](cdn-caching-rules.md). 
- Sorgu dizelerini önbelleğe alma davranışını denetlemek için kullanılacağını öğrenmek için bkz: [denetimi Azure CDN önbelleğe alma davranışını sorgu dizeleriyle](cdn-query-string.md).




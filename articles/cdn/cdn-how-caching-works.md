---
title: Önbelleğe alma nasıl işe yarar? | Microsoft Docs
description: Önbelleğe alma, verileri yerel olarak depolamanın, bu verilere yönelik gelecekteki isteklere daha hızlı erişilebilmesi için bir işlemdir.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: 7a4688c196551f3ab6b5713d8939f53af161d1e3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505017"
---
# <a name="how-caching-works"></a>Önbelleğe alma nasıl işler?

Bu makalede genel önbelleğe alma kavramlarını ve [Azure Content Delivery Network (CDN)](cdn-overview.md) , performansı artırmak için önbelleğe alma özelliğinin bir genel bakışı sunulmaktadır. CDN uç noktanıza önbelleğe alma davranışının nasıl özelleştirileceği hakkında bilgi edinmek istiyorsanız, bkz. önbellek [kuralları ile denetim Azure CDN önbelleğe alma davranışı](cdn-caching-rules.md) ve [sorgu dizeleri ile Azure CDN önbelleğe alma davranışı](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Önbelleğe almaya giriş

Önbelleğe alma, verileri yerel olarak depolamanın, bu verilere yönelik gelecekteki isteklere daha hızlı erişilebilmesi için bir işlemdir. En yaygın önbelleğe alma, Web tarayıcısı önbelleğe alma türünde, bir Web tarayıcısı statik verilerin kopyalarını yerel bir sabit sürücüde yerel olarak depolar. Web tarayıcısı, önbelleğe alma özelliğini kullanarak sunucuda birden çok gidiş dönüş yapmaktan kaçınabilir, bunun yerine aynı verilere yerel olarak erişebilir ve böylece zaman ve kaynak tasarrufu sağlar. Önbelleğe alma, statik görüntüler, CSS dosyaları ve JavaScript dosyaları gibi küçük, statik verilerin yerel olarak yönetilmesi için uygun bir seçenektir.

Benzer şekilde, önbelleğe alma isteklerinin kaynağa geri dönmesi ve son kullanıcı gecikmesini azaltmamak için uç sunuculardaki bir içerik teslim ağı tarafından önbelleğe alma kullanılır. Yalnızca tek bir kullanıcı için kullanılan bir Web tarayıcısı önbelleğinin aksine, CDN paylaşılan bir önbelleğe sahiptir. CDN paylaşılan önbelleğinde, bir kullanıcı tarafından istenen bir dosyaya daha sonra diğer kullanıcılar tarafından erişilebiliyor ve bu da kaynak sunucuya istek sayısını önemli ölçüde düşürür.

Sık sık değişen veya tek bir kullanıcıya özgü olan dinamik kaynaklar önbelleğe alınamaz. Bununla birlikte, bu tür kaynaklar, performans iyileştirmeleri için Azure Content Delivery Network dinamik site hızlandırma (DSA) iyileştirmesinden faydalanabilir.

Önbelleğe alma, kaynak sunucu ile son kullanıcı arasında birden fazla düzeyde olabilir:

- Web sunucusu: paylaşılan bir önbellek kullanır (birden çok kullanıcı için).
- İçerik teslim ağı: paylaşılan bir önbellek kullanır (birden çok kullanıcı için).
- Internet servis sağlayıcısı (ISS): paylaşılan bir önbellek kullanır (birden çok kullanıcı için).
- Web tarayıcısı: özel bir önbellek kullanır (bir kullanıcı için).

Her önbellek genellikle kendi kaynak yeniliği yönetir ve bir dosya eskimiş olduğunda doğrulama gerçekleştirir. Bu davranış, [RFC 7234](https://tools.ietf.org/html/rfc7234)http önbellek belirtiminde tanımlanmıştır.

### <a name="resource-freshness"></a>Kaynak yeniliği

Önbelleğe alınmış bir kaynağın güncel veya eski olduğu (kaynak sunucudaki karşılık gelen kaynakla karşılaştırıldığında) olası olabilir. Bu, içeriğin ne zaman yenileneceğini denetlemek için herhangi bir önbelleğe alma mekanizmasının kullanılması önemlidir. Zaman ve bant genişliği tüketimini kazanmak için, önbelleğe alınmış bir kaynak, her erişildiğinde kaynak sunucudaki sürümle karşılaştırılmaz. Bunun yerine, önbelleğe alınmış bir kaynağın yeni olduğu kabul edildiği sürece en güncel sürüm olduğu varsayılır ve doğrudan istemciye gönderilir. Önbellek ayarı tarafından tanımlanan yaş veya dönemden daha az olduğunda önbelleğe alınmış bir kaynak yeni olarak kabul edilir. Örneğin, bir tarayıcı bir Web sayfasını yeniden yüklediğinde, sabit sürücünüzdeki önbelleğe alınan her kaynağın yeni olduğunu doğrular ve yükler. Kaynak yeni (eski) değilse, sunucudan güncel bir kopya yüklenir.

### <a name="validation"></a>Doğrulama

Bir kaynağın eski olduğu kabul edildiğinde, kaynak sunucunun onu doğrulaması istenir, diğer bir deyişle, önbellekteki verilerin kaynak sunucudaki verilerle aynı olup olmadığını tespit eder. Kaynak sunucu üzerinde dosya değiştirildiyse önbellek, kaynağın sürümünü güncelleştirir. Aksi takdirde, kaynak yeni ise, veriler önce doğrulamadan doğrudan önbellekten dağıtılır.

### <a name="cdn-caching"></a>CDN önbelleğe alma

Önbelleğe alma, bir CDN 'nin teslimi hızlandırmak ve görüntüler, yazı tipleri ve videolar gibi statik varlıkların kaynak yükünü azaltmak için bir CDN 'nin bir yoludur. CDN önbelleğe alma işleminde statik kaynaklar, bir kullanıcıya daha yerel olan ve aşağıdaki avantajları sunan, stratejik olarak yerleştirilmiş olan sunucular üzerinde seçmeli olarak depolanır:

- Çoğu web trafiği statik olduğu için (örneğin, görüntüler, yazı tipleri ve videolar), CDN önbelleğe alma, içeriği kullanıcıya yaklaştırarak ağ gecikmesini azaltır ve bu sayede verilerin nasıl taşdığı mesafeyi azaltır.

- İşi CDN 'ye devrederek, önbelleğe alma işlemi, ağ trafiğini ve kaynak sunucudaki yükü azaltabilir. Bunun yapılması, çok sayıda kullanıcı olsa bile, uygulamanın maliyetini ve kaynak gereksinimlerini azaltır.

Önbelleğe almanın bir Web tarayıcısında uygulanma biçimine benzer şekilde, Cache-Directive üst bilgilerini göndererek bir CDN 'de önbelleğe almanın nasıl gerçekleştirileceğini denetleyebilirsiniz. Cache-Directive üstbilgileri, genellikle kaynak sunucu tarafından eklenen HTTP başlıklardır. Bu üstbilgilerin çoğu başlangıçta istemci tarayıcılarındaki önbelleğe alma için tasarlansa da, bunlar artık CDNs gibi tüm ara önbellekler tarafından da kullanılmaktadır. 

Önbellek yeniliği tanımlamak için iki üst bilgi kullanılabilir: `Cache-Control` ve `Expires` . `Cache-Control` daha güncel olur ve `Expires` her ikisi de varsa önceliklidir. Ayrıca, doğrulama için kullanılan iki tür üstbilgi vardır (doğrulayıcılar adı verilir): `ETag` ve `Last-Modified` . `ETag``Last-Modified`, her ikisi de tanımlıysa daha güncel ve önceliklidir.  

## <a name="cache-directive-headers"></a>Cache-Directive üstbilgileri

> [!IMPORTANT]
> Varsayılan olarak, DSA için iyileştirilmiş bir Azure CDN uç noktası Cache-Directive üst bilgilerini yoksayar ve önbelleğe almayı atlar. Verizon **Azure CDN ve Akamai profillerden standart** **Azure CDN Standart** için, bir Azure CDN uç noktasının önbelleğe almayı etkinleştirmek için [CDN önbelleğe alma kurallarını](cdn-caching-rules.md) kullanarak bu üstbilgileri nasıl değerlendirmiş olduğunu ayarlayabilirsiniz. Yalnızca **Verizon profillerinin Azure CDN Premium** için, önbelleğe almayı etkinleştirmek üzere [Rules altyapısını](./cdn-verizon-premium-rules-engine.md) kullanın.

Azure CDN, önbellek süresini ve önbellek paylaşımını tanımlayan aşağıdaki HTTP Cache-Directive üst bilgilerini destekler.

**Cache-Control:**
- HTTP 1,1 ' de kullanıma sunulmuş, Web yayımcılarının içeriği üzerinde daha fazla denetime sahip olması ve üst bilgi sınırlamalarını ele vermesi `Expires` .
- `Expires`Her ikisi de tanımlıysa üstbilgiyi geçersiz kılar `Cache-Control` .
- İstemciden CDN POP 'a bir HTTP isteğinde kullanıldığında, `Cache-Control` Varsayılan olarak tüm Azure CDN profilleri tarafından yok sayılır.
- İstemciden CDN POP 'a bir HTTP yanıtında kullanıldığında:
     - Verizon **Azure CDN ve Microsoft** desteği tüm yönergelerden standart **/Premium Azure CDN** `Cache-Control` .
     - Verizon ve Microsoft 'tan **Standart/Premium Azure CDN** , [RFC 7234-Hypertext Transfer Protocol (http/1.1): Caching (ietf.org)](https://tools.ietf.org/html/rfc7234#section-5.2.2.8)içindeki Cache-Control yönergeler için önbelleğe alma davranışlarından **Azure CDN** .
     - **Akamai from Azure CDN Standart** yalnızca aşağıdaki yönergeleri destekler `Cache-Control` ; diğerleri yok sayılır:
         - `max-age`: Bir önbellek, belirtilen saniye sayısı için içeriği depolayabilirler. Örneğin, `Cache-Control: max-age=5`. Bu yönerge, içeriğin yeni olarak kabul edildiği en uzun süreyi belirtir.
         - `no-cache`: İçeriği önbelleğe alma, ancak önbellekten teslim etmeden önce her seferinde içerik doğrulama. İle eşdeğerdir `Cache-Control: max-age=0` .
         - `no-store`: İçeriği hiçbir bir şekilde önbelleğe alma. Daha önce depolanmışsa içeriği kaldırın.

**Bitiminden**
- HTTP 1,0 ' de sunulan eski üst bilgi; geriye dönük uyumluluk için desteklenir.
- İkinci duyarlıkla Tarih temelli bir sona erme saati kullanır. 
- Benzer `Cache-Control: max-age` .
- Mevcut olmadığında kullanılır `Cache-Control` .

**Prag**
   - Varsayılan olarak Azure CDN tarafından kabul edilmez.
   - HTTP 1,0 ' de sunulan eski üst bilgi; geriye dönük uyumluluk için desteklenir.
   - Şu yönergeyle istemci isteği üst bilgisi olarak kullanılır: `no-cache` . Bu yönerge, sunucuya kaynağın yeni bir sürümünü sunmasını söyler.
   - `Pragma: no-cache`, `Cache-Control: no-cache` ile eşdeğerdir.

## <a name="validators"></a>Metninin

Önbellek eskidiğinde, bir dosyanın önbelleğe alınmış sürümünü kaynak sunucudaki sürümüyle karşılaştırmak için HTTP önbellek Doğrulayıcıları kullanılır. **Verizon tarafından sunulan standart/Premium** , varsayılan olarak hem hem de `ETag` `Last-Modified` Doğrulayıcıları destekler, **Microsoft 'Tan alınan Azure CDN Standart** ve **Akamai 'den Azure CDN Standart** yalnızca `Last-Modified` Varsayılan olarak destekler. Azure CDN

**Özelliği**
- **Verizon ' dan Standart/Premium Azure CDN** `ETag` Varsayılan olarak destekler, **Microsoft 'Tan Azure CDN standard** ve **Akamai 'den Azure CDN Standart** .
- `ETag` bir dosyanın her bir dosyası ve sürümü için benzersiz olan bir dize tanımlar. Örneğin, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- HTTP 1,1 ' de kullanıma sunulmuştur ve ' den daha güncel `Last-Modified` . Son değiştirme tarihi saptanmaları zor olduğunda faydalıdır.
- Hem güçlü doğrulamayı hem de zayıf doğrulamayı destekler; Ancak, Azure CDN yalnızca güçlü doğrulamayı destekler. Güçlü doğrulama için iki kaynak temsili, bayt için bayt özdeş olmalıdır. 
- Önbellek `ETag` `If-None-Match` , istekte bir veya daha fazla doğrulayıcıya sahip bir üst bilgi göndererek kullanan bir dosyayı doğrular `ETag` . Örneğin, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Sunucunun sürümü listedeki bir doğrulayıcı ile eşleşiyorsa `ETag` , yanıt olarak 304 (değiştirilmez) durum kodunu gönderir. Sürüm farklıysa, sunucu 200 (Tamam) durum koduyla ve güncelleştirilmiş kaynakla yanıt verir.

**Son değiştirme:**
- Yalnızca **Verizon öğesinden Standart/Premium Azure CDN** IÇIN, `Last-Modified` `ETag` HTTP yanıtının bir parçası değilse kullanılır. 
- Kaynak sunucunun, kaynağın en son değiştirildiğini belirlediği tarihi ve saati belirtir. Örneğin, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Önbellek `Last-Modified` `If-Modified-Since` , istekte tarih ve saat içeren bir üstbilgi göndererek kullanarak bir dosyayı doğrular. Kaynak sunucu bu tarihi `Last-Modified` en son kaynağın üstbilgisiyle karşılaştırır. Kaynak belirtilen süreden bu yana değiştirilmediyse, sunucu yanıtında 304 (değiştirilmez) durum kodunu döndürür. Kaynak değiştirildiyse, sunucu 200 (Tamam) durum kodunu ve güncelleştirilmiş kaynağı döndürür.

## <a name="determining-which-files-can-be-cached"></a>Hangi dosyaların önbelleğe alınacağını belirleme

Tüm kaynaklar önbelleğe alınmayabilir. Aşağıdaki tabloda, HTTP yanıtının türüne göre hangi kaynakların önbelleğe alınacağını gösterilmektedir. Bu koşulların tümünü karşılamayan HTTP yanıtlarıyla teslim edilen kaynaklar önbelleğe alınamaz. Yalnızca **Verizon 'den Azure CDN Premium** için, bu koşulların bazılarını özelleştirmek üzere Rules altyapısını kullanabilirsiniz.

|                       | Microsoft 'tan Azure CDN          | Verizon 'dan Azure CDN | Akamai 'dan Azure CDN        |
|-----------------------|-----------------------------------|------------------------|------------------------------|
| **HTTP durum kodu** | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| **HTTP yöntemleri**      | GET, HEAD                         | GET                    | GET                          |
| **Dosya boyutu sınırları**  | 300 GB                            | 300 GB                 | -Genel web teslimi iyileştirmesi: 1,8 GB<br />-Medya akışı iyileştirmeleri: 1,8 GB<br />-Büyük dosya iyileştirmesi: 150 GB |

**Microsoft 'un** önbelleğe alma işleminin bir kaynakta çalışması için, kaynak sunucunun HERHANGI bir baş ve Get http isteklerini desteklemesi ve içerik uzunluğu değerlerinin HERHANGI bir baş ve VARLıK için http yanıtları alması gerekir. Azure CDN BAŞ bir istek için, kaynak sunucunun baş isteği desteklemesi ve bir GET isteği aldığından aynı üst bilgilerle yanıt vermesi gerekir.

## <a name="default-caching-behavior"></a>Varsayılan önbelleğe alma davranışı

Aşağıdaki tabloda Azure CDN ürünlerin varsayılan önbelleğe alma davranışı ve bunların iyileştirmeleri açıklanmaktadır.

|    | Microsoft: genel web teslimi | Verizon: genel web teslimi | Verizon: DSA | Akamai: genel web teslimi | Akamai: DSA | Akamai: büyük dosya indirme | Akamai: genel veya VOD medya akışı |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Kaynak kabul**       | Yes    | Yes   | Hayır   | Yes    | Hayır   | Yes   | Yes    |
| **CDN önbellek süresi** | 2 gün |7 gün | Yok | 7 gün | Yok | 1 gün | 1 yıl |

**Kaynağı** kabul edin: kaynak SUNUCUDAN gelen http yanıtında varsa, desteklenen Cache-Directive üst bilgilerini kabul edilip edilmeyeceğini belirtir.

**CDN önbellek süresi**: bir kaynağın Azure CDN önbelleğe alınma süresini belirtir. Ancak, kabul etme **kaynağı** Evet ise ve kaynak SUNUCUDAN gelen http yanıtı Cache-Directive üstbilgisini içeriyorsa `Expires` `Cache-Control: max-age` , Azure CDN bunun yerine üst bilgi tarafından belirtilen Duration değerini kullanır. 

## <a name="next-steps"></a>Sonraki adımlar

- CDN 'de önbelleğe alma kuralları aracılığıyla varsayılan önbelleğe alma davranışını özelleştirmeyi ve geçersiz kılmayı öğrenmek için bkz. önbellek [kuralları Ile denetim Azure CDN önbelleğe alma davranışı](cdn-caching-rules.md). 
- Önbelleğe alma davranışını denetlemek için Sorgu dizelerini nasıl kullanacağınızı öğrenmek için bkz. [sorgu dizeleri Ile denetim Azure CDN önbelleğe alma davranışı](cdn-query-string.md).

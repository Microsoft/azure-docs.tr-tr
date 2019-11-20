---
title: Premium Verizon'dan Azure CDN kural altyapısı özellikleri | Microsoft Docs
description: Kural altyapısı özellikleri Azure CDN from Verizon Premium için başvuru belgeleri.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 9177ac544c83305ae95ad681d3dc9f84ac64ea36
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593235"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN Verizon Premium kural altyapısı özellikleri

Bu makalede Azure Content Delivery Network (CDN) için ayrıntılı açıklamaları ve kullanılabilir özellikleri listeler [kurallar altyapısı](cdn-verizon-premium-rules-engine.md).

Bir kural üçüncü bölümü özelliğidir. Bir özellik kümesi eşleştirme koşulları tarafından tanımlanan istek türü uygulanan eylem türünü tanımlar.

## <a name="access-features"></a>Özelliklere erişim

Bu özellikler, içeriğe erişimi denetlemek için tasarlanmıştır.

Ad | Amaç
-----|--------
[Erişimini (403)](#deny-access-403) | Bir 403 Yasak yanıtı ile reddedilen tüm istekleri olup olmadığını belirler.
[Belirteç kimlik doğrulaması](#token-auth) | Belirteç tabanlı kimlik doğrulaması için bir istek uygulandığını belirler.
[Belirteç kimlik doğrulama reddi kod](#token-auth-denial-code) | Belirteç tabanlı kimlik doğrulama nedeniyle bir istek reddedildiğinde kullanıcıya döndürülür yanıtının türünü belirler.
[Belirteç kimlik doğrulaması, URL çalışması yoksay](#token-auth-ignore-url-case) | Belirteç tabanlı kimlik doğrulaması yapılan URL karşılaştırmalar büyük küçük harfe duyarlı olup olmadığını belirler.
[Belirteç kimlik doğrulaması parametresi](#token-auth-parameter) | Belirteç tabanlı kimlik doğrulaması sorgu dizesi parametresinin adı olup olmadığını belirler.

## <a name="caching-features"></a>Önbelleğe alma özellikleri

Bu özellikler, ne zaman ve nasıl içeriği önbelleğe özelleştirmek için tasarlanmıştır.

Ad | Amaç
-----|--------
[Bant genişliği parametreleri](#bandwidth-parameters) | Bant genişliği azaltma parametreler (örneğin, ec_rate ve ec_prebuf) etkin olup olmadığını belirler.
[Bant genişliği azaltma](#bandwidth-throttling) | Sağlanan noktası bulunma tarafından (POP) yanıt için bant genişliği kısıtlar.
[Bypass Cache](#bypass-cache) | İstek önbelleğe almayı Atla gerekmediğini belirler.
[Cache-Control üst bilgisi işleme](#cache-control-header-treatment) | Oluşturulmasını denetleyen `Cache-Control` başlığına göre POP dış Max-Age özelliği etkin olduğunda.
[Önbellek anahtarı sorgu dizesi](#cache-key-query-string) | Önbellek anahtarı içerir veya dışlar bir istekle ilişkili sorgu dizesi parametreleri olup olmadığını belirler.
[Önbellek anahtarı yeniden yazma](#cache-key-rewrite) | Bir istekle ilişkili önbellek anahtarı yeniden yazar.
[Önbellek dolgu tamamlayın](#complete-cache-fill) | POP üzerinde kısmi önbellek isabetsizliği istek sonuçlarını olduğunda ne olacağını belirler.
[Sıkıştırma dosya türleri](#compress-file-types) | Sıkıştırılmış dosyalar için dosya biçimlerini sunucuda tanımlar.
[Varsayılan iç Maksimum yaş](#default-internal-max-age) | Varsayılan, max-age aralığı POP için kaynak sunucusu önbellek yeniden. doğrulama için belirler.
[Üst bilgi işleme süresi](#expires-header-treatment) | Oluşturulmasını denetleyen `Expires` başlığına göre POP dış Max-Age özelliği etkin olduğunda.
[Dış Maksimum yaş](#external-max-age) | POP önbelleği yeniden doğrulama tarayıcıya max-age aralığını belirler.
[İç Max-Age zorla](#force-internal-max-age) | Max-age aralığı POP için kaynak sunucusu önbellek yeniden. doğrulama için belirler.
[H.264 desteği (HTTP aşamalı indirme)](#h264-support-http-progressive-download) | İçerik akışı için kullanılabilir H.264 dosya biçimleri türlerini belirler.
[Uy No-Cache isteği](#honor-no-cache-request) | Bir HTTP istemci no-cache istekleri kaynak sunucuya iletilip iletilmeyeceğini belirler.
[Kaynak No-Cache yoksay](#ignore-origin-no-cache) | CDN bir kaynak sunucudan sunulan belirli yönergeleri saymayacağını belirler.
[Unsatisfiable aralıkları yoksay](#ignore-unsatisfiable-ranges) | Bir isteği 416 İstenen aralık yeterli değil bir durum kodu oluşturduğunda, istemcilere döndürülen yanıt belirler.
[İç Max-eski](#internal-max-stale) | POP kaynak sunucu ile önbelleğe alınan varlık düzeltin işlenemediğinde denetimleri ne kadar süreyle normal sona erme süresini geçen bir önbelleğe alınan varlık POP hizmet alırlar.
[Kısmi önbellek paylaşımı](#partial-cache-sharing) | İstek kısmen önbelleğe alınmış içerikleri oluşturmak olup olmadığını belirler.
[Önbelleğe alınmış içerikleri prevalidate](#prevalidate-cached-content) | Kendi TTL süresi dolmadan önce önbelleğe alınmış içerikleri erken yeniden doğrulanması uygun olup olmadığını belirler.
[Sıfır bayt önbellek dosyalarını Yenile](#refresh-zero-byte-cache-files) | 0 bayt önbellek varlık için bir HTTP istemci isteği Pop'lere tarafından nasıl işlendiğini belirler.
[Önbelleğe alınabilir durum kodları](#set-cacheable-status-codes) | Önbelleğe alınan içeriği sonuçlanabilir durum kodları kümesi tanımlar.
[Eski bir içerik teslim hata](#stale-content-delivery-on-error) | Talep edilen içeriği müşteri kaynak sunucudan alınırken bir hata önbellek yeniden doğrulama sırasında veya ortaya çıktığında önbelleğe alınmış içerikleri teslim süresi olup olmadığını belirler.
[Revalidate getirse](#stale-while-revalidate) | Yeniden doğrulama gerçekleşirken eski istemci istemciye hizmet Pop'lere vererek performansını artırır.

## <a name="comment-feature"></a>Açıklama özelliği

Bu özellik, bir kural içindeki ek bilgileri sağlamak için tasarlanmıştır.

Ad | Amaç
-----|--------
[Açıklama](#comment) | İçinde bir kural eklenecek bir not sağlar.

## <a name="header-features"></a>Üstbilgi Özellikleri

Bu özellikler, eklemek, değiştirmek veya istek veya yanıt üst bilgileri silmek için tasarlanmıştır.

Ad | Amaç
-----|--------
[Age yanıtı üstbilgisi](#age-response-header) | İstek sahibine gönderilen yanıtta bir Age yanıtı üstbilgisi içerilip içerilmeyeceğini belirler.
[Önbellek yanıt üstbilgilerini hata ayıklama](#debug-cache-response-headers) | Yanıt, istenen varlık için önbellek İlkesi hakkında bilgi sağlayan X-EC-Debug yanıt üst bilgisi içerebilir olup olmadığını belirler.
[İstemci istek üst bilgisini değiştirin](#modify-client-request-header) | Geçersiz kılar, ekler veya bir istekten bir üst bilgisi siler.
[İstemci yanıt üst bilgisi değiştirme](#modify-client-response-header) | Geçersiz kılar, ekler veya bir üst bilgi yanıtı siler.
[İstemci IP özel üst bilgisini ayarlayın](#set-client-ip-custom-header) | İsteği bir özel istek üstbilgisi olarak eklenmesi için istekte bulunan istemciye IP adresi sağlar.

## <a name="logging-features"></a>Günlüğe kaydetme özellikleri

Bu özellikler, ham günlük dosyalarında depolanan verileri özelleştirmek için tasarlanmıştır.

Ad | Amaç
-----|--------
[Özel günlük alan 1](#custom-log-field-1) | Biçim ve ham günlük dosyası özel günlük alana atanan içerikler belirler.
[Günlük sorgu dizesi](#log-query-string) | Bir sorgu dizesi erişim günlükleri URL'de birlikte depolanan olup olmadığını belirler.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled


### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Kaynak özellikleri

Bu özellikler, CDN, kaynak sunucu ile nasıl iletişim kurduğu denetlemek için tasarlanmıştır.

Ad | Amaç
-----|--------
[En fazla Canlı istek](#maximum-keep-alive-requests) | Kapalı olduğu önce en fazla canlı bağlantı için istek sayısını tanımlar.
[Proxy özel üst bilgileri](#proxy-special-headers) | POP bir kaynak sunucuya iletilir CDN özgü istek üstbilgilerini kümesini tanımlar.

## <a name="specialty-features"></a>Özel Özellikler

Bu özellikler, İleri düzey kullanıcılar için gelişmiş işlevsellik sağlar.

Ad | Amaç
-----|--------
[Önbelleğe alınabilir HTTP yöntemleri](#cacheable-http-methods) | Ağ üzerinde önbelleğe ek HTTP yöntemleri kümesini belirler.
[Önbelleğe alınabilir istek gövdesi boyutu](#cacheable-request-body-size) | Bir GÖNDERİ yanıtı önbelleğe olup olmadığını belirlemek için eşiği tanımlar.
[Kullanıcı değişkeni](#user-variable) | Yalnızca iç kullanım içindir.

## <a name="url-features"></a>URL özellikleri

Bu özellikler, isteği yeniden yönlendirilen ya da farklı bir URL'ye yeniden olanak sağlar.

Ad | Amaç
-----|--------
[Yeniden yönlendirmeleri izleyin](#follow-redirects) | Bir müşteri kaynak sunucu tarafından döndürülen konum üst bilgisi içinde tanımlı ana bilgisayar adı istekleri yönlendirilebilir olup olmadığını belirler.
[URL yeniden yönlendirme](#url-redirect) | Location üst bilgisini keşfi yönlendirir.
[URL yeniden yazma](#url-rewrite)  | İstek URL'si yeniden yazar.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Azure CDN'den Verizon Premium kural altyapısı özellikleri başvurusu

---

### <a name="age-response-header"></a>Age yanıtı üstbilgisi

**Amaç**: İstek sahibine gönderilen yanıtta bir Age yanıtı üstbilgisi içerilip içerilmeyeceğini belirler.

Value|Sonuç
--|--
Enabled | Age yanıtı üstbilgisi istek sahibine gönderilen yanıt dahil edilir.
Devre dışı | Age yanıtı üstbilgisi istek sahibine gönderilen yanıtından çıkarılır.

**Varsayılan davranış**: Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Bant genişliği parametreleri

**Amaç:** Bant genişliği azaltma parametreler (örneğin, ec_rate ve ec_prebuf) etkin olup olmadığını belirler.

Bant genişliği azaltma parametreleri istemci isteği için veri aktarım hızı için özel bir oranı sınırlı olup olmadığını belirler.

Değer|Sonuç
--|--
Enabled|Bant genişliği azaltma isteği kabul etmenin Pop'lere sağlar.
Devre dışı|Bant genişliği azaltma parametreler yok sayılacak Pop'lere neden olur. Talep edilen içeriği normalde hizmet (diğer bir deyişle, bant genişliği azaltma olmadan).

**Varsayılan davranışı:** Etkin.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Bant genişliği azaltma

**Amaç:** POP tarafından sağlanan yanıt için bant genişliği kısıtlar.

Aşağıdaki seçeneklerden birini hem de bant genişliği azaltma yukarı doğru şekilde ayarlamak için tanımlanmalıdır.

Seçenek|Açıklama
--|--
Saniye başına kilobayt|Yanıt sunmak için kullanılabilecek en fazla bant genişliği (Kb / saniye) bu seçeneği ayarlayın.
Prebuf saniye|Bu seçenek POP'ları bant genişliği daraltma kadar beklenecek saniye sayısını ayarlayın. Bu süre içinde sınırsız bant genişliği amacı, bir medya yürütücüsü görüntüsü gidip gelir veya arabelleğe alma sorunları, bant genişliği azaltma nedeniyle yaşamasını engellemek sağlamaktır.

**Varsayılan davranışı:** Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Önbelleğini atla

**Amaç:** İstek önbelleğe almayı Atla gerekmediğini belirler.

Value|Sonuç
--|--
Enabled|İçerik POP'ları üzerinde önceden önbelleğe alınmış olsa bile kaynak sunucuya geçiş tüm istekleri neden olur.
Devre dışı|POP önbellek varlıklara göre kendi yanıt üst bilgilerinde tanımlanmış önbellek İlkesi neden olur.

**Varsayılan davranışı:**

- **HTTP büyük:** Devre dışı

<!---
- **ADN:** Enabled

--->

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Önbelleğe alınabilir HTTP yöntemleri

**Amaç:** Ağ üzerinde önbelleğe ek HTTP yöntemleri kümesini belirler.

Anahtar bilgileri:

- Bu özellik GET yanıtlar her zaman önbelleğe varsayar. Sonuç olarak, GET HTTP yöntemini bu özelliği ayarlanırken dahil olmamalıdır.
- Bu özellik yalnızca POST HTTP yöntemini destekler. Bu özellik ayarlayarak POST yanıt önbelleğe almayı etkinleştir `POST`.
- Varsayılan olarak, yalnızca gövde 14 KB'den küçük isteklerini önbelleğe alınır. En büyük istek gövdesi boyutunu ayarlamak için önbelleğe istek gövdesi boyutu özelliğini kullanın.

**Varsayılan davranışı:** Yalnızca GET yanıtları önbelleğe alınır.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Önbelleğe alınabilir istek gövdesi boyutu

**Amaç:** Bir GÖNDERİ yanıtı önbelleğe olup olmadığını belirlemek için eşiği tanımlar.

Bu eşik, en fazla istek gövdesi boyutu belirterek belirlenir. Daha büyük bir istek gövdesi içeren istekleri önbelleğe alınmaz.

Anahtar bilgileri:

- Bu özellik yalnızca POST yanıtları önbelleğe alma işlemi için uygun olduğunda geçerlidir. POST isteğini önbelleğe almayı etkinleştirmek için önbelleğe HTTP yöntemleri özelliğini kullanın.
- İstek gövdesi için dikkate alınır:
    - x-www-form-urlencoded işlemek değerleri
    - Benzersiz bir önbellek anahtar sağlama
- Büyük en büyük istek gövdesi boyutu tanımlayarak veri teslim performansını etkileyebilir.
    - **Önerilen değer:** 14 Kb
    - **Minimum değer:** 1 Kb

**Varsayılan davranışı:** 14 Kb

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Cache-Control üst bilgisi işleme

**Amaç:** Oluşturulmasını denetleyen `Cache-Control` başlığına göre POP dış Max-Age özelliği etkin olduğunda.

Bu tür bir yapılandırma yapmanın en kolay yolu dış Max-Age ve Cache-Control üst bilgisi işleme özellikleri aynı deyimde yerleştirmektir.

Value|Sonuç
--|--
Üzerine yaz|Aşağıdaki eylemler gerçekleşir sağlar:<br/> -Yazar `Cache-Control` kaynak sunucu tarafından oluşturulan üstbilgi. <br/>-Ekler `Cache-Control` üstbilgi üretilen yanıta dış Max-Age özelliği.
Geçişi|Sağlar `Cache-Control` dış Max-Age özelliği tarafından üretilen üst bilgi yanıtı hiçbir zaman eklenir. <br/> Kaynak sunucu oluşturursa bir `Cache-Control` başlık arabimini aracılığıyla son kullanıcıya. <br/> Kaynak sunucu değil oluşturmak, bir `Cache-Control` üst bilgi, ardından bu seçeneği yanıt üst bilgisi olmayan öğeler neden olabilir bir `Cache-Control` başlığı.
Eksikse Ekle|Varsa bir `Cache-Control` üst bilgisi kaynak sunucudan alınmadı, ardından bu seçeneği ekler `Cache-Control` üst bilgisi dış Max-Age özelliği tarafından üretilen. Bu seçenek tüm varlıkları atanan sağlamak için yararlı bir `Cache-Control` başlığı.
Kaldır| Bu seçenek sağlar bir `Cache-Control` üst bilgisi ile üst bilgi yanıtı dahil değildir. Varsa bir `Cache-Control` üstbilgi zaten atanmış ve ardından üst bilgi yanıtı kaldırılır.

**Varsayılan davranışı:** Üzerine yazın.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Önbellek anahtarı sorgu dizesi

**Amaç:** Önbellek anahtarı içerir veya dışlar bir istekle ilişkili sorgu dizesi parametreleri olup olmadığını belirler.

Anahtar bilgileri:

- Bir veya daha fazla sorgu dizesi parametresi adları belirtin ve parametre adları tek bir boşluk ile ayırın.
- Bu özellik, sorgu dizesi parametreleri dahil veya önbellek anahtarından dışlanan olup olmadığını belirler. Aşağıdaki tabloda her seçeneğe ilişkin ek bilgiler sağlanmaktadır.

Type|Açıklama
--|--
 İçerir|  Belirtilen her parametre önbellek anahtarını dahil olduğunu gösterir. Bu özellik içinde tanımlanan bir sorgu dizesi parametresi için benzersiz bir değer içeren her istek için benzersiz bir önbellek anahtarı oluşturulur.
 Tüm ekleme  |Benzersiz sorgu dizesi içeren bir varlık için her istek için benzersiz bir önbellek anahtarı oluşturulduğunu gösterir. Küçük bir önbellek isabet yüzdesi neden olabileceği için bu yapılandırma türü genellikle önerilmez. Daha fazla isteklere hizmet gerekir çünkü düşük bir önbellek isabet sayısı, kaynak sunucu üzerindeki yükü artırır. Bu yapılandırma, "benzersiz-cache" sorgu dizesi önbelleğe alma sayfasında olarak bilinen bir önbelleğe alma davranışı çoğaltır.
 Hariç tutma | Yalnızca belirtilen parametre önbellek anahtarından hariç tutulduğu gösterir. Tüm diğer sorgu dizesi parametreleri, önbellek anahtarını dahil edilir.
 Tümünü hariç tut  |Tüm sorgu dizesi parametreleri önbelleğe anahtarından dışlanmaz gösterir. Bu yapılandırma, önbelleğe alma davranışını sorgu dizesini önbelleğe alma sayfasında "önbellek standart" varsayılan çoğaltır.  

Kural altyapısı içinde sorgu dizesini önbelleğe alma uygulanan şekilde özelleştirmenizi sağlar. Örneğin, sorgu dizesini önbelleğe alma yalnızca belirli konumları veya dosya türleri üzerinde gerçekleştirildiğini belirtebilirsiniz.

Önbelleğe alma davranışını sorgu dizesini önbelleğe alma sayfasında "no-cache" sorgu dizesi yinelenen için URL sorgu joker eşleşme koşulu ve atlama önbellek özelliği içeren bir kural oluşturun. URL sorgu joker eşleşme koşulu için bir yıldız işareti (*) ayarlayın.

>[!IMPORTANT]
> Bu hesapta herhangi bir yol için yetkilendirme belirteci etkinleştirilirse, standart Önbellek modu sorgu dizesini önbelleğe alma için kullanılabilen tek moddur. Daha fazla bilgi için bkz. [Sorgu dizeleri içeren Azure CDN önbelleğe alma davranışını kontrol etme](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Örnek senaryolar

Aşağıdaki örnek kullanım için bu özellik, bir örnek istek ve varsayılan önbellek anahtarı sağlar:

- **Örnek istek:** http://wpc.0001.&lt ; etki alanı&gt; /800001/Origin/folder/asset.htm?sessionid=1234 & dili = tr & UserID = 01
- **Varsayılan önbellek anahtarı:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>İçerir

Örnek Yapılandırması:

- **Türü:** İçerir
- **Parametreleri:** dil

Bu tür bir yapılandırma aşağıdaki sorgu dizesi parametresi önbellek anahtar oluşturur:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Tüm ekleme

Örnek Yapılandırması:

- **Türü:** Tüm ekleme

Bu tür bir yapılandırma aşağıdaki sorgu dizesi parametresi önbellek anahtar oluşturur:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Hariç tutma

Örnek Yapılandırması:

- **Türü:** Hariç tutma
- **Parametreleri:** sessioned UserID

Bu tür bir yapılandırma aşağıdaki sorgu dizesi parametresi önbellek anahtar oluşturur:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Tümünü hariç tut

Örnek Yapılandırması:

- **Türü:** Tümünü hariç tut

Bu tür bir yapılandırma aşağıdaki sorgu dizesi parametresi önbellek anahtar oluşturur:

    /800001/Origin/folder/asset.htm

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Önbellek anahtarı yeniden yazma

**Amaç:** Bir istekle ilişkili önbellek anahtarı yeniden yazar.

Önbellek anahtarı önbelleğe alma amacıyla bir varlığı tanımlayan göreli yoludur. Diğer bir deyişle, sunucuların bir varlık yolu göre önbelleğe alınmış bir sürümü, önbellek anahtarı tarafından tanımlandığı şekilde kontrol edin.

Bu özellik, her ikisi de aşağıdaki seçeneklerden birini tanımlayarak yapılandırın:

Seçenek|Açıklama
--|--
Özgün yolu| Göreli yol için önbellek anahtarını yeniden istek türlerini tanımlayın. Göreli bir yol, temel kaynak yolu seçerek ve ardından bir normal ifade deseni tanımlayan tanımlanabilir.
Yeni yol|Yeni önbellek anahtarı için göreli yolu tanımlayın. Göreli bir yol, temel kaynak yolu seçerek ve ardından bir normal ifade deseni tanımlayan tanımlanabilir. Bu göreli yolu kullanarak dinamik olarak oluşturulabilir [HTTP değişkenleri](cdn-http-variables.md).

**Varsayılan davranışı:** Bir isteğin önbellek anahtarı, istek URI'si tarafından belirlenir.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Yorum

**Amaç:** İçinde bir kural eklenecek bir not sağlar.

Genel amaçlı bir kural ya da neden belirli bir koşul eşleşen veya özellik kuralı eklendiği hakkında ek bilgi sağlamak için bu özellik bir kullanım içindir.

Anahtar bilgileri:

- En fazla 150 karakter belirtilebilir.
- Yalnızca alfasayısal karakterler kullanın.
- Bu özellik, kural davranışını etkilemez. Yalnızca, burada bilgi için ileride ya da kural gidermede yardımcı olabilecek sağlayabilir bir alanı sağlamak için tasarlanmıştır.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Önbellek dolgu tamamlayın

**Amaç:** POP üzerinde kısmi önbellek isabetsizliği istek sonuçlarını olduğunda ne olacağını belirler.

Kısmi önbellek isabetsizliği tamamen POP'a yüklenmedi bir varlık için önbellek durumu açıklar. Bir varlığı kısmen POP üzerinde önbelleğe alınmışsa bir sonraki istek için o varlığı yeniden kaynak sunucuya iletilir.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Kısmi önbellek isabetsizliği genellikle bir kullanıcı bir indirme iptal sonra veya yalnızca HTTP aralığı isteklerini kullanarak istenen varlıklar için gerçekleşir. Bu özellik genellikle (örneğin, videoları) baştan sona yüklenmez büyük varlıklar için kullanışlıdır. Sonuç olarak, bu özellik HTTP büyük platformu üzerinde varsayılan olarak etkinleştirilir. Diğer tüm platformlarda devre dışı bırakıldı.

HTTP büyük bir platform için varsayılan yapılandırma, müşteri kaynak sunucu üzerindeki yükü azaltır ve, müşterilerinizin içerik indirme hızını artırır çünkü tutun.

Value|Sonuç
--|--
Enabled|Varsayılan davranışını geri yükler. Varlık kaynak sunucusundan bir arka planda getirme başlatmak için POP zorlamak için varsayılan davranıştır. Sonra varlık POP'ın yerel önbellek üzerinde olacaktır.
Devre dışı|POP, varlık için bir arka planda getirme gerçekleştirmesini engeller. Bir sonraki istek için o varlığı o bölgenin müşteri kaynak sunucudan istemek POP neden sonucudur.

**Varsayılan davranışı:** Etkin.

#### <a name="compatibility"></a>Uyumluluk

Hangi önbellek ayarları izlenen şekilde nedeniyle, bu özellik aşağıdaki eşleşme koşullarla ilişkilendirilemez:

- Sayı olarak
- İstemci IP adresi
- Tanımlama bilgisi parametresi
- Tanımlama bilgisi parametre normal ifade
- Country
- Cihaz
- Microsoft Edge Cname
- Etki alanı başvuran
- İstek üst bilgisi değişmez değeri
- İstek üst bilgisi normal ifade
- İstek üst bilgisi joker karakter
- İstek yöntemi
- İstek düzeni
- URL sorgu değişmez değeri
- Sorgu Regex URL'si
- URL sorgu joker karakter
- URL sorgu parametresi

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Sıkıştırma dosya türleri

**Amaç:** Sıkıştırılmış dosyalar için dosya biçimlerini sunucuda tanımlar.

Bir dosya biçimi, Internet medya türünü (örneğin, Content-Type) kullanılarak belirtilebilir. Internet medya türü belirli bir varlık dosya biçimini belirlemek için sunucuları veren platformdan bağımsız meta verilerdir. Ortak Internet medya türlerinin bir listesi aşağıda verilmiştir.

Internet medya türü|Açıklama
--|--
metin/düz|Düz metin dosyaları
metin/html| HTML dosyaları
metin/css|Geçişli stil sayfaları (CSS)
Application/x-javascript|Javascript
Uygulama/javascript|Javascript

Anahtar bilgileri:

- Birden çok Internet medya türleri, her biri tek bir boşluk ile sınırlayan tarafından belirtin.
- Bu özellik yalnızca, boyutu 1 MB'tan az varlıkları sıkıştırır. Daha büyük varlıklar sunucuları tarafından sıkıştırılmaz.
- Resim, video ve gibi içerikleri ve ses medya varlıkları (örneğin, JPG, MP3, MP4 vb.), belirli bir türdeki zaten sıkıştırılmış. Bu tür varlıklar ek sıkıştırma dosya boyutunu önemli ölçüde etkilememesini nedeniyle sıkıştırma bunları özelliğini etkinleştirmemeniz önerilir.
- Yıldız işaretleri gibi joker karakterler desteklenmez.
- Bu özellik için bir kural eklemeden önce sıkıştırma sayfasında bu kuralın uygulandığı bir platform için sıkıştırmayı devre dışı seçeneğini belirlediğiniz emin olun.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Özel günlük alan 1

**Amaç:** Biçim ve ham günlük dosyası özel günlük alana atanmış içerik belirler.

Bu özel alan, günlük dosyalarında depolanan hangi istek ve yanıt üstbilgi değerlerini belirlemenize olanak sağlar.

Varsayılan olarak, özel günlük alan "x-ec_custom-1." olarak adlandırılır Bu alanın adı ham günlük ayarları sayfasından özelleştirilebilir.

İstek ve yanıt üst bilgileri belirtmek için biçim şu şekilde tanımlanır:

Üst bilgi türü|Biçimi|Örnekler
-|-|-
İstek üstbilgisi|`%{[RequestHeader]()}[i]()` | % {Kabul-Encoding} ediyorum <br/> {Referrer}i <br/> % {Yetkilendirme} i
Yanıt Üst Bilgisi|`%{[ResponseHeader]()}[o]()`| % {Yaş} o <br/> %{Content-Type}o <br/> % {Tanımlama bilgisi} o

Anahtar bilgileri:

- Özel günlük alanı, üst bilgi alanları ve düz metin herhangi bir birleşimini içerebilir.
- Bu alanın geçerli karakterler şunlardır: alfasayısal (0-9, a-z ve A-Z), tire, iki nokta üst üste, noktalı, kesme, virgül, nokta, alt çizgi, eşittir işareti, parantez, köşeli ayraçlar ve alanları. Küme ayraçları ve yüzde simgesi yalnızca bir başlık alanını belirlemek için kullanıldığında izin verilir.
- Her belirtilen üstbilgi alanı için yazım istenen istek/yanıt üstbilgisi adı eşleşmelidir.
- Birden çok üst bilgi belirtmek istiyorsanız, her bir üst bilgi belirtmek için ayırıcı kullanın. Örneğin, her bir üst bilgisi için bir kısaltma kullanabilirsiniz:
    - AE: % {kabul-Encoding} i y: % {Yetkilendirme} i CT: % {Content-Type} o

**Varsayılan değer:**  -

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Önbellek yanıt üstbilgilerini hata ayıklama

**Amaç:** Bir yanıt içerip içeremeyeceğini belirleyen [X-EC-Debug yanıt üstbilgilerini](cdn-http-debug-headers.md), sağlayan bilgi önbellek ilkesi için istenen varlık.

Hata ayıklama önbellek yanıtı aşağıdakilerin her ikisi de true olduğunda yanıtta üst bilgiler dahil edilir:

- İstekte belirtilen önbellek yanıt üst bilgileri hata ayıklama özelliği etkinleştirildi.
- Belirtilen isteği, yanıta dahil edilecek hata ayıklama önbellek yanıt üstbilgilerini kümesini tanımlar.

Hata ayıklama önbellek yanıt üst bilgileri, aşağıdaki üst bilgi ve belirtilen yönergeleri istekte dahil ederek istenebilir:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Örnek:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Value|Sonuç
-|-
Enabled|Hata ayıklama önbellek yanıt üstbilgileri için istekleri X-EC-Debug üst bilgi içeren bir yanıt döndürür.
Devre dışı|X-EC-Debug yanıt üst bilgisi yanıttan edilmeyecek.

**Varsayılan davranışı:** Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Varsayılan iç Maksimum yaş

**Amaç:** Varsayılan, max-age aralığı POP için kaynak sunucusu önbellek yeniden. doğrulama için belirler. Diğer bir deyişle, POP önce geçecek süreyi önbelleğe alınan varlık, kaynak sunucuda depolanan varlık eşleşip eşleşmediğini kontrol eder.

Anahtar bilgileri:

- Bu eylem yalnızca yanıtlarının, max-age bir gösterge içinde atamadığınız bir kaynak sunucudan gerçekleşecek `Cache-Control` veya `Expires` başlığı.
- Bu eylem, önbelleğe sayılan olmayan varlıklar için yer olmayacaktır.
- Bu eylem, POP önbellek revalidations tarayıcıya etkilemez. Bu tür revalidations tarafından belirlenen `Cache-Control` veya `Expires` dış Max-Age özelliğiyle özelleştirilebilir tarayıcıya gönderilen üst bilgiler.
- Bu eylemin sonuçlarını yanıt üstbilgileri ve POP'larından içeriğiniz için döndürülen içerik gözlemlenebilir bir etkisi yoktur, ancak POP'larından kaynak sunucunuza gönderilen yeniden doğrulama trafik miktarı üzerinde bir etkisi olabilir.
- Bu özellik tarafından yapılandırın:
    - Bir varsayılan iç Maksimum yaş uygulanabilir durum kodu seçme.
    - Bir tamsayı değeri belirtme ve sonra istediğiniz zaman birimini (örneğin, saniye, dakika, saat, vb.) seçerek. Bu değer varsayılan iç max-age aralığı tanımlar.

- Zaman birimi "Kapalı" ayarını bir varsayılan iç max-age aralığı 7 günü istekler için Maksimum yaş bir gösterge olarak atanmamış atama kendi `Cache-Control` veya `Expires` başlığı.

**Varsayılan değer:** 7 gün

#### <a name="compatibility"></a>Uyumluluk

Hangi önbellek ayarları izlenen şekilde nedeniyle, bu özellik aşağıdaki eşleşme koşullarla ilişkilendirilemez:
- Sayı olarak
- İstemci IP adresi
- Tanımlama bilgisi parametresi
- Tanımlama bilgisi parametre normal ifade
- Country
- Cihaz
- Edge Cname
- Etki alanı başvuran
- İstek üst bilgisi değişmez değeri
- İstek üst bilgisi normal ifade
- İstek üst bilgisi joker karakter
- İstek yöntemi
- İstek düzeni
- URL sorgu değişmez değeri
- Sorgu Regex URL'si
- URL sorgu joker karakter
- URL sorgu parametresi

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Deny Access (403)

**Amaç**: Bir 403 Yasak yanıtı ile reddedilen tüm istekleri olup olmadığını belirler.

Value | Sonuç
------|-------
Enabled| Bir 403 Yasak yanıtı ile reddedilir eşleştirme ölçütü karşılayan tüm isteklerin neden olur.
Devre dışı| Varsayılan davranışını geri yükler. Döndürülecek yanıt türünü belirlemek kaynak sunucuya izin vermek için varsayılan davranıştır.

**Varsayılan davranış**: Devre dışı

> [!TIP]
   > İçeriğinize satır içi bağlantıları kullanarak HTTP başvuran erişimi engellemek için bir istek üst bilgisi eşleşme koşulu olarak ilişkilendirmek için bu özellik için olası kullanım var.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Üst bilgi işleme süresi

**Amaç:** Oluşturulmasını denetleyen `Expires` başlığına göre POP dış Max-Age özelliği etkin olduğunda.

Bu tür bir yapılandırma yapmanın en kolay yolu dış Max-Age ve Expires üst bilgisi işleme özellikleri aynı deyimde yerleştirmektir.

Değer|Sonuç
--|--
Üzerine yaz|Aşağıdaki eylemler gerçekleşir sağlar:<br/>-Yazar `Expires` kaynak sunucu tarafından oluşturulan üstbilgi.<br/>-Ekler `Expires` üstbilgi üretilen yanıta dış Max-Age özelliği.
Geçişi|Sağlar `Expires` dış Max-Age özelliği tarafından üretilen üst bilgi yanıtı hiçbir zaman eklenir. <br/> Kaynak sunucu oluşturursa bir `Expires` üst bilgi, geçecek aracılığıyla son kullanıcıya. <br/>Kaynak sunucu değil oluşturmak, bir `Expires` üst bilgi, ardından bu seçeneği yanıt üst bilgisi olmayan öğeler neden olabilir bir `Expires` başlığı.
Eksikse Ekle| Varsa bir `Expires` üst bilgisi kaynak sunucudan alınmadı, ardından bu seçeneği ekler `Expires` üst bilgisi dış Max-Age özelliği tarafından üretilen. Bu seçenek tüm varlıkları atanacak sağlamak için yararlı bir `Expires` başlığı.
Kaldır| Sağlar bir `Expires` üst bilgisi ile üst bilgi yanıtı dahil değildir. Varsa bir `Expires` üstbilgi zaten atanmış ve ardından üst bilgi yanıtı kaldırılır.

**Varsayılan davranışı:** Üzerine yaz

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Dış Maksimum yaş

**Amaç:** POP önbelleği yeniden doğrulama tarayıcıya max-age aralığını belirler. Diğer bir deyişle, bir tarayıcı önce geçecek süreyi POP'den bir varlığın yeni bir sürümü denetleyebilirsiniz.

Bu özelliğin etkinleştirilmesi oluşturacağını `Cache-Control: max-age` ve `Expires` Pop'lere üst bilgiler ve bunları göndermek için HTTP istemcisi. Varsayılan olarak, bu üst kaynak sunucusu tarafından oluşturulan bu üstbilgileri üzerine yazar. Ancak, Cache-Control üst bilgisi işleme ve Expires üst bilgisi işleme özellikleri bu davranışı değiştirmek için kullanılabilir.

Anahtar bilgileri:

- Bu eylem için kaynak sunucusu önbellek revalidations POP etkilemez. Bu tür revalidations tarafından belirlenen `Cache-Control` ve `Expires` üst bilgiler kaynak sunucudan alınan ve varsayılan iç Max-Age ve zorla iç Max-Age özelliklerle özelleştirilebilir.
- Bu özellik, bir tamsayı değeri belirterek ve istediğiniz zaman birimini (örneğin, saniye, dakika, saat, vb.) seçerek yapılandırın.
- Bu özelliği negatif bir değere ayarlanması neden Pop'lere göndermek bir `Cache-Control: no-cache` ve `Expires` tarayıcıya her yanıt geçmişte ayarlanan zaman. Bir HTTP istemci yanıtı önbelleğe almaz ancak bu ayar POP kaynak sunucudan yanıt önbelleğe alma yeteneğini etkilemez.
- Zaman birimi "Kapalı" ayarı, bu özelliği devre dışı bırakır. `Cache-Control` Ve `Expires` kaynak sunucu yanıtı ile önbelleğe alınmış üstbilgileri geçecek aracılığıyla tarayıcıya.

**Varsayılan davranışı:** Kapalı

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Yeniden yönlendirmeleri izleyin

**Amaç:** Bir müşteri kaynak sunucu tarafından döndürülen konum üst bilgisi içinde tanımlı ana bilgisayar adı istekleri yönlendirilebilir olup olmadığını belirler.

Anahtar bilgileri:

- İstekleri, yalnızca aynı platform için karşılık gelen CNAME'ler uca yönlendirilebilir.

Value|Sonuç
-|-
Enabled|İstekleri yeniden yönlendirilebilir.
Devre dışı|İstekleri yeniden yönlendirilmeyecek.

**Varsayılan davranışı:** Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>İç Max-Age zorla

**Amaç:** Max-age aralığı POP için kaynak sunucusu önbellek yeniden. doğrulama için belirler. Diğer bir deyişle, POP önce geçecek süreyi önbelleğe alınan varlık, kaynak sunucuda depolanan varlık eşleşip eşleşmediğini kontrol edebilirsiniz.

Anahtar bilgileri:

- Bu özellik, max-age aralığı içinde tanımlı geçersiz kılacak olan `Cache-Control` veya `Expires` bir kaynak sunucudan oluşturulan üst bilgileri.
- Bu özellik, POP önbellek revalidations tarayıcıya etkilemez. Bu tür revalidations tarafından belirlenen `Cache-Control` veya `Expires` tarayıcıya gönderilen üst bilgiler.
- Bu özellik tarafından POP istemciye teslim yanıt gözlemlenebilir bir etkisi yok. Ancak, bunu POP'larından kaynak sunucuya gönderilen yeniden doğrulama trafik miktarı üzerinde bir etkisi olabilir.
- Bu özellik tarafından yapılandırın:
    - Bir iç max-age uygulanacak durum kodu seçme.
    - Bir tamsayı değeri belirterek ve istediğiniz zaman birimini (örneğin, saniye, dakika, saat, vb.) seçme. Bu değer, isteğin max-age aralığı tanımlar.

- Bu özellik zaman birimi "Kapalı" ayarını devre dışı bırakır. Bir iç max-age aralığı için istenilen varlıkların atanmaz. Özgün üstbilgisi önbelleğe alma yönergeleri içermiyorsa, ardından varlık varsayılan iç Max-Age özelliği etkin ayarına göre önbelleğe alınır.

**Varsayılan davranışı:** Kapalı

#### <a name="compatibility"></a>Uyumluluk

Hangi önbellek ayarları izlenen şekilde nedeniyle, bu özellik aşağıdaki eşleşme koşullarla ilişkilendirilemez:
- Sayı olarak
- İstemci IP adresi
- Tanımlama bilgisi parametresi
- Tanımlama bilgisi parametre normal ifade
- Country
- Cihaz
- Edge Cname
- Etki alanı başvuran
- İstek üst bilgisi değişmez değeri
- İstek üst bilgisi normal ifade
- İstek üst bilgisi joker karakter
- İstek yöntemi
- İstek düzeni
- URL sorgu değişmez değeri
- Sorgu Regex URL'si
- URL sorgu joker karakter
- URL sorgu parametresi

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H.264 desteği (HTTP aşamalı indirme)

**Amaç:** İçerik akışı için kullanılabilir H.264 dosya biçimleri türlerini belirler.

Anahtar bilgileri:

- İzin verilen H.264 dosya adı uzantıları boşlukla ayrılmış bir dizi için dosya uzantıları seçeneğinde tanımlayın. Dosya uzantıları seçeneği, varsayılan davranışı geçersiz kılar. Bu dosya adı uzantıları dahil ederek, bu seçeneği ayarlarken MP4 ve F4V desteği korur.
- Her bir dosya adı uzantısı belirtirseniz bir süre içerir (örneğin, _.mp4_, _.f4v_).

**Varsayılan davranışı:** HTTP aşamalı indirme MP4 ve F4V ortamı varsayılan olarak destekler.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Uy No-Cache isteği

**Amaç:** Bir HTTP istemci no-cache istekleri kaynak sunucuya iletilir olup olmadığını belirler.

Bir no-cache isteği HTTP istemcisi gönderdiğinde oluşur bir `Cache-Control: no-cache` ve/veya `Pragma: no-cache` HTTP isteği üstbilgisi.

Value|Sonuç
--|--
Enabled|Bir HTTP istemci no-cache istekleri kaynak sunucuya iletilmesi için ve kaynak sunucu HTTP istemcisine geri yanıt üst bilgileri ve gövdesini POP aracılığıyla döndüreceği sağlar.
Devre dışı|Varsayılan davranışını geri yükler. No-cache istekleri kaynak sunucuya iletilmesini önlemek için varsayılan davranıştır.

Tüm üretim trafiği için bu özellik, varsayılan devre dışı durumda bırakılmasını önemle tavsiye edilir. Aksi takdirde, kaynak sunucu son kullanıcılar, birçok no-cache istekleri web sayfaları yenileme esnasında oluşacak yanlışlıkla tetikleyebilir veya bir no-cache üstbilgisi video her istekle göndermesini kodlanmış birçok popüler medya oynatıcıları korunmasına değil. Bununla birlikte, bu özellik belirli dizinleri, isteğe bağlı kaynak sunucudan çekilmesi yeni içerik izin vermek üzere test veya hazırlama üretim dışı uygulamak yararlı olabilir.

Bu özellik nedeniyle bir kaynak sunucuya iletilen bir istek için bildirilen önbellek durumu `TCP_Client_Refresh_Miss`. Çekirdek modülü raporlama kullanılabilir önbellek durumları raporu tarafından önbellek durumu istatistiksel bilgi sağlar. Bu rapor sayısı ve yüzdesi iletilen istekler, bu özellik nedeniyle bir kaynak sunucuya izlemenize olanak tanır.

**Varsayılan davranışı:** Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Kaynak No-Cache yoksay

**Amaç:** Bir kaynak sunucudan sunulan aşağıdaki yönergeleri CDN yoksayıp yoksaymadığını belirler:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Anahtar bilgileri:

- Yukarıdaki yönergeleri yoksayılır durum kodları, boşlukla ayrılmış listesini tanımlayarak bu özelliği yapılandırın.
- Bu özellik için geçerli durum kodları kümesi şunlardır: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 ve 505.
- Bu özellik boş bir değere ayarlayarak devre dışı bırakın.

**Varsayılan davranışı:** Yukarıdaki yönergeleri uymanız varsayılan davranışıdır.

#### <a name="compatibility"></a>Uyumluluk

Hangi önbellek ayarları izlenen şekilde nedeniyle, bu özellik aşağıdaki eşleşme koşullarla ilişkilendirilemez:
- Sayı olarak
- İstemci IP adresi
- Tanımlama bilgisi parametresi
- Tanımlama bilgisi parametre normal ifade
- Country
- Cihaz
- Edge Cname
- Etki alanı başvuran
- İstek üst bilgisi değişmez değeri
- İstek üst bilgisi normal ifade
- İstek üst bilgisi joker karakter
- İstek yöntemi
- İstek düzeni
- URL sorgu değişmez değeri
- Sorgu Regex URL'si
- URL sorgu joker karakter
- URL sorgu parametresi

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Unsatisfiable aralıkları yoksay

**Amaç:** Bir isteği 416 İstenen aralık yeterli değil bir durum kodu oluşturduğunda, istemcilere döndürülen yanıt belirler.

Varsayılan olarak, belirtilen bayt aralığı istek POP tarafından karşılanamayan ve bir IF-Range isteği üstbilgisi alanının belirtilmemiş olduğunda bu durum kodu döndürülür.

Değer|Sonuç
-|-
Enabled|POP 416 İstenen aralık yeterli değil bir durum koduna sahip bir geçersiz bayt aralığı isteğine yanıt vermesini engeller. Bunun yerine sunucu istenen varlık teslim ve 200 Tamam istemciye döndürür.
Devre dışı|Varsayılan davranışını geri yükler. İstenen aralık yeterli değil 416 durum kodunu uymanız varsayılan davranışıdır.

**Varsayılan davranışı:** Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>İç Max-eski

**Amaç:** POP kaynak sunucu ile önbelleğe alınan varlık düzeltin işlenemediğinde denetimleri ne kadar süreyle normal sona erme süresini geçen bir önbelleğe alınan varlık POP hizmet alırlar.

Normalde, bir varlığın max-age süresi dolduğunda, POP kaynak sunucuya yeniden doğrulama isteği gönderir. Kaynak sunucusu olacak sonra bir ya da 304 ile Yanıtla POP yeni vermek için değişiklik kira önbelleğe alınan varlık üzerinde or else 200 Tamam POP önbelleğe alınan varlık güncelleştirilmiş bir sürümünü sağlamak için.

POP bu iç Max-eski özellik olup olmadığı ve ne kadar süreyle denetler sonra böyle bir yeniden doğrulama, çalışırken kaynak sunucu ile bağlantı kuramadı, POP şimdi eski varlık hizmet devam edebilir.

Varlığın, max-age dolduğunda değil başarısız yeniden doğrulama oluştuğunda bu zaman aralığı başlatır. Bu nedenle, bu sırada başarılı yeniden doğrulama bir varlık sunulabilecek en uzun süresi, max-age yanı sıra en fazla eski birleşimi tarafından belirtilen süre miktarıdır. 9:00 ile 30 dakika Maksimum yaş ve en fazla eski 15 dakikalık bir varlık önbelleğe alınmışsa, örneğin, ardından bir başarısız yeniden doğrulama girişiminde 9:44 9:46 başarısız yeniden doğrulama teşebbüs tr neden olur ancak eski önbelleğe alınan varlık alma bir son kullanıcının neden olur 504 ağ geçidi zaman aşımı alma d kullanıcı.

Bu özellik yerine geçen için yapılandırılmış herhangi bir değer `Cache-Control: must-revalidate` veya `Cache-Control: proxy-revalidate` üstbilgiler kaynak sunucudan alındı. Bu üst bilgi ya da alındığında, kaynak sunucudan bir varlık başlangıçta önbelleğe alındığında POP eski bir önbelleğe alınan varlık davranacak değil. POP varlığın max-age aralığının süresi dolduğunda, kaynak düzeltin kaydedemediği böyle bir durumda bulunma Noktasındaki bir 504 ağ geçidi zaman aşımı hatası döndürür.

Anahtar bilgileri:

- Bu özellik tarafından yapılandırın:
    - En fazla eski uygulanacak durum kodu seçme.
    - Bir tamsayı değeri belirtme ve sonra istediğiniz zaman birimini (örneğin, saniye, dakika, saat, vb.) seçerek. Bu değer, iç max uygulanacak eski tanımlar.

- Zaman birimi "Kapalı" ayarı, bu özelliği devre dışı bırakır. Önbelleğe alınan varlık normal geçerlilik süresi dışında hizmet yok.

**Varsayılan davranışı:** İki dakika

#### <a name="compatibility"></a>Uyumluluk

Hangi önbellek ayarları izlenen şekilde nedeniyle, bu özellik aşağıdaki eşleşme koşullarla ilişkilendirilemez:
- Sayı olarak
- İstemci IP adresi
- Tanımlama bilgisi parametresi
- Tanımlama bilgisi parametre normal ifade
- Country
- Cihaz
- Edge Cname
- Etki alanı başvuran
- İstek üst bilgisi değişmez değeri
- İstek üst bilgisi normal ifade
- İstek üst bilgisi joker karakter
- İstek yöntemi
- İstek düzeni
- URL sorgu değişmez değeri
- Sorgu Regex URL'si
- URL sorgu joker karakter
- URL sorgu parametresi

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Günlük sorgu dizesi

**Amaç:** Bir sorgu dizesi erişim günlükleri URL'de birlikte depolanan olup olmadığını belirler.

Değer|Sonuç
-|-
Enabled|Sorgu dizeleri depolama URL'leri bir erişim günlüğe kaydederken sağlar. Bir URL bir sorgu dizesi içermiyorsa, ardından bu seçeneği bir etkisi yoktur.
Devre dışı|Varsayılan davranışını geri yükler. URL'leri bir erişim günlüğe kaydederken sorgu dizelerini yoksay için varsayılan davranıştır.

**Varsayılan davranışı:** Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>En fazla Canlı istek

**Amaç:** Kapalı olduğu önce en fazla canlı bağlantı için istek sayısını tanımlar.

En fazla istek sayısını düşük bir değere ayarlanması önerilmez ve performans düşüşüne neden olabilir.

Anahtar bilgileri:

- Bu değer bir tamsayı belirtin.
- Belirtilen değer, nokta veya virgül eklemeyin.

**Varsayılan değer:** 10.000 istekleri

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>İstemci istek üst bilgisini değiştirin

**Amaç:** Her isteği istek üstbilgileri, onu tanımlayan bir dizi içeriyor. Bu özellik şunlardan birini yapabilirsiniz:

- Ekleme veya bir istek üst bilgisi için atanan değer üzerine yazabilirsiniz. Belirtilen istek üstbilgisi mevcut değilse, ardından bu özellik, isteği ekler.
- İstek üstbilgisi istekten silin.

Bir kaynak sunucusuna iletilen istekler, bu özellik tarafından yapılan değişiklikleri yansıtır.

Aşağıdaki eylemlerden birini istek üst bilgisinde gerçekleştirilebilir:

Seçenek|Açıklama|Örnek
-|-|-
Ekle|Belirtilen değer var olan istek üst bilgisi değerinin sonuna eklenir.|**İstek üst bilgisi değeri (istemci):**<br/>Değer1<br/>**İstek üst bilgisi değeri (Kural altyapısı):**<br/>Value2 <br/>**Yeni istek üst bilgi değeri:** <br/>Value1Value2
Üzerine yaz|İstek üst bilgisi değeri belirtilen değere ayarlanır.|**İstek üst bilgisi değeri (istemci):**<br/>Değer1<br/>**İstek üst bilgisi değeri (Kural altyapısı):**<br/>Value2<br/>**Yeni istek üst bilgi değeri:**<br/> Value2 <br/>
Sil|Belirtilen istek üst bilgisi siler.|**İstek üst bilgisi değeri (istemci):**<br/>Değer1<br/>**İstemci isteği üst bilgisi yapılandırması değiştirin:**<br/>Söz konusu istek üst bilgisini silin.<br/>**Sonuç:**<br/>Belirtilen istek üst bilgisi, kaynak sunucuya iletilen değil.

Anahtar bilgileri:

- Adı seçeneğinde belirtilen değeri istenen istek üstbilgisi için tam bir eşleşme olduğundan emin olun.
- Servis talebi üstbilgi tanımlamak amacıyla hesaba katılmaz. Örneğin, herhangi bir aşağıdaki çeşitleri `Cache-Control` üst bilgi adı tanımlamak için kullanılabilir:
    - önbellek denetimi
    - ÖNBELLEK DENETİMİ
    - Önbellek denetimi
- Üst bilgi adı belirtilirken, yalnızca alfasayısal karakterler, tire ve alt çizgi kullanın.
- Bir üst bilgisi siliniyor Pop'lere tarafından bir kaynak sunucusuna iletilen engeller.
- Şu ayrılmış ve tarafından bu özellik değiştirilemez:
    - iletilen
    - host
    - aracılığıyla
    - uyarı
    - x-iletilen-için
    - "X-ec" ile başlayan tüm üst bilgi adları ayrılmıştır.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>İstemci yanıt üst bilgisi değiştirme

Her yanıt, yanıt üstbilgilerini, onu tanımlayan bir kümesini içerir. Bu özellik şunlardan birini yapabilirsiniz:

- Ekleme veya bir yanıt üstbilgisi için atanan değer üzerine yazabilirsiniz. Belirtilen yanıt üst bilgisi yoksa, ardından bu özellik, yanıta ekler.
- Yanıt üst bilgisi yanıttan silin.

Varsayılan olarak, yanıt üstbilgi değerleri kaynak sunucu ve Pop'lere tarafından tanımlanır.

Aşağıdaki eylemlerden birini bir yanıt üstbilgisi gerçekleştirilebilir:

Seçenek|Açıklama|Örnek
-|-|-
Ekle|Belirtilen değer, mevcut yanıt üst bilgisi değeri sonuna eklenir.|**Yanıt üst bilgisi değeri (istemci):**<br />Değer1<br/>**Yanıt üst bilgisi değeri (Kural altyapısı):**<br/>Value2<br/>**Yeni yanıt üstbilgi değeri:**<br/>Value1Value2
Üzerine yaz|Yanıt üst bilgisi değeri belirtilen değere ayarlanır.|**Yanıt üst bilgisi değeri (istemci):**<br/>Değer1<br/>**Yanıt üst bilgisi değeri (Kural altyapısı):**<br/>Value2 <br/>**Yeni yanıt üstbilgi değeri:**<br/>Value2 <br/>
Sil|Belirtilen yanıt üstbilgisinin siler.|**Yanıt üst bilgisi değeri (istemci):**<br/>Değer1<br/>**İstemci yanıtı üstbilgisi yapılandırmasını değiştirin:**<br/>Yanıt üst bilgisi söz konusu silin.<br/>**Sonuç:**<br/>Belirtilen yanıt üstbilgisinin istemciye iletilecek değil.

Anahtar bilgileri:

- Adı seçeneğinde belirtilen değer'istenen yanıt üstbilgisi için tam bir eşleşme olduğundan emin olun.
- Servis talebi üstbilgi tanımlamak amacıyla hesaba katılmaz. Örneğin, herhangi bir aşağıdaki çeşitleri `Cache-Control` üst bilgi adı tanımlamak için kullanılabilir:
    - önbellek denetimi
    - ÖNBELLEK DENETİMİ
    - Önbellek denetimi
- Bir üst bilgisi siliniyor, istemciye iletilen engeller.
- Şu ayrılmış ve tarafından bu özellik değiştirilemez:
    - kabul kodlama
    - Geçerlilik süresi
    - bağlantı
    - İçerik kodlama
    - içerik uzunluğu
    - İçerik-aralık
    - date
    - server
    - Tanıtım
    - Transfer-encoding
    - upgrade
    - değişiklik
    - aracılığıyla
    - uyarı
    - "X-ec" ile başlayan tüm üst bilgi adları ayrılmıştır.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Kısmi önbellek paylaşımı

**Amaç:** İstek kısmen önbelleğe alınmış içerikleri oluşturmak olup olmadığını belirler.

Kısmi Bu önbellek, ardından istenen içeriğin tam olarak önbelleğe kadar bu içeriği için yeni isteklerini karşılamak için kullanılabilir.

Value|Sonuç
-|-
Enabled|İstekleri kısmen önbelleğe alınmış içeriği oluşturabilirsiniz.
Devre dışı|İstekleri yalnızca istenen içeriğin tam olarak önbelleğe alınmış bir sürümü oluşturabilirsiniz.

**Varsayılan davranışı:** Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Önbelleğe alınmış içerikleri prevalidate

**Amaç:** Kendi TTL süresi dolmadan önce önbelleğe alınmış içerikleri erken yeniden doğrulanması uygun olup olmadığını belirler.

Önce sona erme tarihini istenen içeriğin TTL, erken yeniden doğrulanması uygun olacağı süreyi tanımlar.

Anahtar bilgileri:

- "Kapalı" yeniden doğrulama önbelleğe alınan içeriğin sonra gerçekleşmesi için zaman birimi olarak seçerek, TTL süresi doldu. Zaman belirtilmemesi gerekir ve göz ardı edilir.

**Varsayılan davranışı:** Kapalı. Önbelleğe alınan içeriğin TTL süresi dolduktan sonra yeniden doğrulama yalnızca yer alabilir.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Proxy özel üst bilgileri

**Amaç:** Kümesini tanımlayan [Verizon'a özgü HTTP istek üstbilgilerinin](cdn-verizon-http-headers.md) , iletilir POP bir kaynak sunucuya.

Anahtar bilgileri:

- Bu özelliği tanımlanan her CDN özgü istek üst bilgisi bir kaynak sunucuya iletilir. Hariç tutulan üstbilgileri iletilmez.
- CDN özel istek üstbilgisi iletilmesini önlemek için üst liste alanda boşlukla ayrılmış listesinden kaldırın.

Aşağıdaki HTTP üst bilgilerini varsayılan listede yer:
- aracılığıyla
- X-iletilen-için
- X iletilen Proto
- X-Host
- X-Midgress
- X-Gateway-List
- X-EC-Name
- Ana bilgisayar

**Varsayılan davranışı:** Tüm CDN özgü istek üst bilgilerini kaynak sunucuya iletilir.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Sıfır bayt önbellek dosyalarını Yenile

**Amaç:** 0 bayt önbellek varlık için bir HTTP istemci isteği Pop'lere tarafından nasıl işlendiğini belirler.

Geçerli değerler şunlardır:

Value|Sonuç
--|--
Enabled|Varlık kaynak sunucudan öğeleri tekrar Al POP'a neden olur.
Devre dışı|Varsayılan davranışını geri yükler. İstek üzerine geçerli önbellek kıymetler hizmet için varsayılan davranıştır.

Bu özellik, doğru önbelleğe alma ve içerik teslimi için gerekli değildir, ancak geçici bir çözüm olarak yararlı olabilir. Örneğin, kaynak sunucularda dinamik içerik oluşturucuları yanlışlıkla Pop'lere gönderilen bayt 0 yanıtları neden olabilir. Bu tür yanıtların POP'ları genellikle önbelleğe alınır. 0 bayt yanıt hiçbir zaman bu içerik için geçerli bir yanıt olduğunu biliyorsanız, bu özellik bu tür varlıklar istemcilerinize hizmet engelleyebilirsiniz.

**Varsayılan davranışı:** Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Önbelleğe alınabilir durum kodları

**Amaç:** Önbelleğe alınan içeriği sonuçlanabilir durum kodları kümesi tanımlar.

Varsayılan olarak, önbelleğe yalnızca 200 Tamam yanıtlar için etkin.

İstenen durum kodları boşlukla ayrılmış bir kümesini tanımlar.

Anahtar bilgileri:

- Kaynak No-Cache yoksay özelliğini etkinleştirin. Bu özellik etkin değilse, ardından 200 Tamam yanıtları önbelleğe alınabilir değil.
- Bu özellik için geçerli durum kodları kümesi şunlardır: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 ve 505.
- Bu özellik, bir 200 Tamam durum kodu oluşturmak için yanıtları önbelleğe alma devre dışı bırakmak için kullanılamaz.

**Varsayılan davranışı:** Önbelleğe alma, bir 200 Tamam durum kodu oluşturan yanıtlar için etkindir.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>İstemci IP özel üst bilgisini ayarlayın

**Amaç:** İstekte bulunan istemciye isteğine IP adresine göre tanımlayan özel bir başlık ekler.

Üst bilgi adı seçeneği, istemcinin IP adresini depolandığı özel istek üst bilgisi adını tanımlar.

Bu özellik, bir müşteri sağlar. istemci IP adresi bulmak için kaynak sunucu adresleri özel istek üstbilgisi. İstek Önbelleği'ndeki sunulur, kaynak sunucu istemcinin IP adresini bildirilmez. Bu nedenle, bu özellik önbelleğe olmayan varlıklar ile kullanılması önerilir.

Belirtilen üst bilgi adı aşağıdaki adları hiçbirini eşleşmediğini emin olun:

- Standart istek üst bilgi adları. Standart üst bilgi adları listesini bulunabilir [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Ayrılmış üst bilgi adları:
    - iletilen için
    - host
    - değişiklik
    - aracılığıyla
    - uyarı
    - x-iletilen-için
    - "X-ec" ile başlayan tüm üst bilgi adları ayrılmıştır.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Eski bir içerik teslim hata

**Amaç:** Talep edilen içeriği müşteri kaynak sunucudan alınırken bir hata önbellek yeniden doğrulama sırasında veya ortaya çıktığında önbelleğe alınmış içerikleri teslim süresi olup olmadığını belirler.

Value|Sonuç
-|-
Enabled|Bir kaynak sunucuya bağlanma sırasında bir hata oluştuğunda eski içeriği istemciye hizmet verir.
Devre dışı|Kaynak sunucunun hata olarak iletilir.

**Varsayılan davranışı:** Devre dışı

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Revalidate getirse

**Amaç:** Yeniden doğrulama gerçekleşirken eski içeriği istemciye hizmet Pop'lere vererek performansını artırır.

Anahtar bilgileri:

- Bu özelliği davranışı, seçili zaman birimi göre değişir.
    - **Zaman birimi:** Bir süre belirtin ve eski bir içerik teslim izin vermek için zaman birimi (örneğin, saniye, dakika, saat, vb.) seçin. Bu tür bir kurulum doğrulama şu formüle göre gerektirmeden önce teslim edebilir sürenin uzunluğunu genişletmek CDN içerik sağlar: **TTL** + **Revalidate zaman sırasında eski**
    - **Kapalı:** "Kapalı" Eski içerik için bir istek sunulan önce yeniden doğrulama gerektirecek şekilde seçin.
        - Geçerli değil ve yok sayılacak bir süre boyunca belirtmeyin.

**Varsayılan davranışı:** Kapalı. Talep edilen içeriği sunulabilen önce yeniden doğrulama gerçekleşmesi gerekir.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Belirteç kimlik doğrulaması

**Amaç:** Belirteç tabanlı kimlik doğrulaması için bir istek uygulanacak olup olmadığını belirler.

Belirteç tabanlı kimlik doğrulaması etkinleştirilirse, şifrelenmiş bir belirteç sağlayın ve bu belirteci tarafından belirtilen gereksinimlere uyması yalnızca istek getirilmez.

Şifreleme ve şifre çözme belirteci değerleri için kullanılan şifreleme anahtarını birincil anahtarını ve belirteç kimlik doğrulaması sayfasında anahtarını yedekleme seçenekleri tarafından belirlenir. Şifreleme anahtarları, platforma özgü olduğunu aklınızda bulundurun.

**Varsayılan davranışı:** Devre dışı.

Bu özellik URL yeniden yazma özelliği hariç olmak üzere çoğu özelliği daha önceliklidir.

Value | Sonuç
------|---------
Enabled | Belirteç tabanlı kimlik doğrulaması ile istenen içeriği korunur. Yalnızca geçerli bir belirteç sağlayın ve kendi gereksinimlerini istemcilerden gelen istekleri kabul edilir. FTP işlemleri, belirteç tabanlı kimlik doğrulamasını bırakılır.
Devre dışı| Varsayılan davranışını geri yükler. Bir isteğin güvenli olup olmadığını belirlemek belirteç tabanlı kimlik doğrulaması yapılandırmanızı izin vermek için varsayılan davranıştır.

#### <a name="compatibility"></a>Uyumluluk

Belirteç kimlik doğrulaması ile bir her zaman eşleşme koşulu kullanmayın.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Belirteç kimlik doğrulama reddi kod

**Amaç:** Belirteç tabanlı kimlik doğrulama nedeniyle bir istek reddedildiğinde, kullanıcıya döndürülecek yanıt türünü belirler.

Aşağıdaki tabloda kullanılabilir yanıt kodları listelenir.

Yanıt Kodu|Yanıt adı|Açıklama
-------------|-------------|--------
301|Kalıcı olarak taşındı|Bu durum kodu yetkisiz kullanıcıların konum üst bilgisinde belirtilen URL'ye yeniden yönlendirir.
302|Bulunamadı|Bu durum kodu yetkisiz kullanıcıların konum üst bilgisinde belirtilen URL'ye yeniden yönlendirir. Bu durum kodu bir yeniden yönlendirme gerçekleştiren sektörde standart yöntemdir.
307|Geçici yeniden yönlendirme|Bu durum kodu yetkisiz kullanıcıların konum üst bilgisinde belirtilen URL'ye yeniden yönlendirir.
401|Yetkilendirilmemiş|Bu durum kodu WWW-Authenticate yanıt üst bilgisi ile birleştiren bir kullanıcıdan kimlik doğrulaması sağlar.
403|Yasak|Bu ileti, yetkisiz bir kullanıcı, korumalı içeriğe erişmeye çalışırken göreceği standart 403 Yasak durum iletisi içindir.
404|Dosya bulunamadı|Bu durum kodu sunucusuyla iletişim kurmak HTTP istemcisi alabildiği, ancak talep edilen içeriği bulunamadı gösterir.

#### <a name="compatibility"></a>Uyumluluk

Belirteç kimlik doğrulama reddi kodu ile bir her zaman eşleşme koşulu kullanmayın. Bunun yerine, **reddi özel işleme** konusundaki **belirteç kimlik doğrulaması** sayfasının **Yönet** portalı. Daha fazla bilgi için [belirteç kimlik doğrulaması ile güvenli hale getirme Azure CDN varlıklar](cdn-token-auth.md).

#### <a name="url-redirection"></a>URL yeniden yönlendirmesi

3xx durum kodunu döndürmek için yapılandırıldığında bu özellik, kullanıcı tanımlı bir URL yeniden yönlendirme URL'sini destekler. Aşağıdaki adımları uygulayarak bu kullanıcı tarafından tanımlanan URL'yi belirtilebilir:

1. Belirteç kimlik doğrulama reddi kod özelliği 3xx yanıt kodunu seçin.
2. "Konum" isteğe bağlı üst bilgi adı seçeneği seçin.
3. İsteğe bağlı üst bilgi değeri seçeneği olarak istenen URL'ye ayarlayın.

Bir URL için 3xx durum kodu tanımlanmazsa, 3xx durum kodu için standart yanıt sayfası kullanıcıya döndürülür.

URL yeniden yönlendirmesi, yalnızca 3xx yanıt kodları için geçerlidir.

İsteğe bağlı üst bilgi değeri seçenek alfasayısal karakterler, tırnak işareti ve boşluk destekler.

#### <a name="authentication"></a>Authentication

Bu özellik, WWW-Authenticate üstbilgisi için belirteç tabanlı kimlik doğrulaması tarafından korunan içeriği yetkisiz bir isteğe yanıt verirken ekleyin yeteneğini destekler. WWW-Authenticate üstbilgisi yapılandırmanızda "temel" olarak ayarlanmışsa, yetkisiz bir kullanıcı hesabı kimlik bilgileri istenir.

Yukarıdaki yapılandırma aşağıdaki adımları uygulayarak elde edilebilir:

1. "401" belirteci kimlik doğrulama reddi kod özelliği için yanıt kodunu seçin.
2. "WWW-Authenticate" isteğe bağlı üst bilgi adı seçeneği seçin.
3. "Temel" isteğe bağlı üst bilgi değeri seçeneğine ayarlayın

WWW-Authenticate üstbilgisi yalnızca 401 yanıt kodları için geçerlidir.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Belirteç kimlik doğrulaması, URL çalışması yoksay

**Amaç:** Belirteç tabanlı kimlik doğrulaması yapılan URL karşılaştırmalar büyük küçük harfe duyarlı olup olmadığını belirler.

Bu özellik tarafından etkilenen Parametreler şunlardır:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Geçerli değerler şunlardır:

Value|Sonuç
---|----
Enabled|URL'ler için belirteç tabanlı kimlik doğrulama parametreleri karşılaştırılırken durumu yok saymak POP neden olur.
Devre dışı|Varsayılan davranışını geri yükler. URL karşılaştırmalar büyük küçük harfe duyarlı olması belirteci kimlik doğrulaması için varsayılan davranıştır.

**Varsayılan davranışı:** Devre dışı.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Belirteç kimlik doğrulaması parametresi

**Amaç:** Belirteç tabanlı kimlik doğrulaması sorgu dizesi parametresinin adı olup olmadığını belirler.

Anahtar bilgileri:

- Değer seçeneği bir belirteç belirtilebilir sorgu dizesi parametresinin adını tanımlar.
- Değer seçeneği "ec_token" ayarlanamaz
- Değer seçeneği tanımlanan adın yalnızca geçerli URL karakterlerini içerdiğinden emin olun.

Value|Sonuç
----|----
Enabled|Değer seçeneği belirteçleri tanımlanmalıdır sorgu dizesi parametresinin adını tanımlar.
Devre dışı|İstek URL'si içinde tanımlanmamış bir sorgu dizesi parametresi olarak bir belirteç belirtilebilir.

**Varsayılan davranışı:** Devre dışı. İstek URL'si içinde tanımlanmamış bir sorgu dizesi parametresi olarak bir belirteç belirtilebilir.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>URL yeniden yönlendirme

**Amaç:** Location üst bilgisini keşfi yönlendirir.

Bu özelliğin yapılandırma aşağıdaki ayarları gerektirir:

Seçenek|Açıklama
-|-
Kod|İstemciye döndürülecek yanıt kodunu seçin.
Kaynak & düzeni| Bu ayarları yönlendirilebilirsiniz istek türlerini tanımlayan bir istek URI desenini tanımlar. Yalnızca istek URL'si hem de aşağıdaki ölçütleri karşılayan yönlendirilir: <br/> <br/> **Kaynak (veya içerik erişim noktası):** Kaynak sunucu tanımlayan bir göreli yol seçin. Bu yol _/XXXX/_ bölümü ve uç nokta adınız. <br/><br/> **Kaynak (desen):** Göreli yol istek tanımlayan bir desenle tanımlanması gerekir. Bu normal ifade deseni, doğrudan başlatır (yukarıya bakın) sonra daha önce seçilen içerik erişim noktası bir yol tanımlamanız gerekir. <br/> -Daha önce tanımlanan istek URI ölçütlerini (diğer bir deyişle, kaynak ve desen) çakışmadığını, bu özellik için tanımlanan herhangi bir eşleşme durumu ile emin olun. <br/> -Bir desen belirtin; boş değer deseni olarak kullanırsanız, tüm dizeleri eşleştirilir.
Hedef| Yukarıdaki istekleri yönlendirilecek URL tanımlayın. <br/><br/> Bu URL'yi kullanarak dinamik olarak oluşturun: <br/> -Bir normal ifade deseni <br/>- [HTTP değişkenleri](cdn-http-variables.md) <br/><br/> Kaynak düzende $ kullanarak hedef modele yakalanan değerler yerine_n_ burada _n_ bunu yakalanan sıralama ölçütü bir değer tanımlar. Örneğin, 1 USD $2 ikinci değer temsil ederken, kaynak desende yakalanan ilk değeri temsil eder. <br/>

Bir mutlak URL kullanılacak önemle tavsiye edilir. Bir göreli URL kullanımı için geçersiz bir yol CDN URL'leri yönlendirebilir.

**Örnek senaryo**

Bu örnekte, bir kenar temel bu CDN URL'ye çözümler CNAME URL yeniden yönlendirme işlemini gösterir: http:\//marketing.azureedge.net/brochures

İstekleri uygun bu temel edge CNAME URL yönlendirilirsiniz: http:\//cdn.mydomain.com/resources

Bu URL yeniden yönlendirmesi aşağıdaki yapılandırma elde edilebilir: ![URL yeniden yönlendirme](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Önemli noktalar:**

- İstek URL'sini yeniden yönlendirme özelliğini tanımlar yönlendirileceği URL. Sonuç olarak, ek eşleştirme koşulları gerekli değildir. Eşleşme koşulu "her zaman" olarak tanımlandı ancak "broşürler" klasörü "Pazarlama" Müşteri kaynağı işaret eden istekleri yönlendirilirsiniz.
- Eşleşen tüm istekleri hedef seçeneğinde CNAME URL tanımlanan ölçekten uca yönlendirilir.
    - Örnek Senaryo #1:
        - Örnek istek (CDN URL): http:\//marketing.azureedge.net/brochures/widgets.pdf
        - İstek URL'si (yeniden yönlendirme sonra): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Örnek Senaryo #2:
        - Örnek istek (Edge CNAME URL): http:\//marketing.mydomain.com/brochures/widgets.pdf
        - İstek URL'si (yeniden yönlendirme sonra): http:\//cdn.mydomain.com/resources/widgets.pdf örnek senaryosu
    - Örnek Senaryo #3:
        - Örnek istek (Edge CNAME URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt
        - İstek URL'si (yeniden yönlendirme sonra): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- İsteğin şeması sonra yeniden yönlendirme değişmeden kalmasını sağlar hedef seçeneğinde istek düzeni (% {scheme}) değişkeni yararlanır.
- İstekten yakalanan URL kesimleri "$1." aracılığıyla yeni URL'sine eklenir

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL Yeniden Yazma

**Amaç:** İstek URL'si yeniden yazar.

Anahtar bilgileri:

- Bu özelliğin yapılandırma aşağıdaki ayarları gerektirir:

Seçenek|Açıklama
-|-
 Kaynak & düzeni | Bu ayarları yazılması istek türlerini tanımlayan bir istek URI desenini tanımlar. Yalnızca istek URL'si hem de aşağıdaki ölçütleri karşılayan yazılacaktır: <br/><br/>  - **Kaynak (veya içerik erişim noktası):** Kaynak sunucu tanımlayan bir göreli yol seçin. Bu yol _/XXXX/_ bölümü ve uç nokta adınız. <br/><br/> - **Kaynak (desen):** Göreli yol istek tanımlayan bir desenle tanımlanması gerekir. Bu normal ifade deseni, doğrudan başlatır (yukarıya bakın) sonra daha önce seçilen içerik erişim noktası bir yol tanımlamanız gerekir. <br/> Daha önce tanımlanan istek URI ölçütlerini (diğer bir deyişle, kaynak ve desen) çakışmadığını, bu özellik için tanımlanan eşleşme koşullardan herhangi biri ile doğrulayın. Bir desen belirtin; boş değer deseni olarak kullanırsanız, tüm dizeleri eşleştirilir.
 Hedef  |Yukarıdaki istekleri için tarafından yazılacak göreli URL tanımlayın: <br/>    1. Kaynak sunucu tanımlayan bir içerik erişim noktası seçme. <br/>    2. Bir göreli yol kullanarak tanımlama: <br/>        -Bir normal ifade deseni <br/>        - [HTTP değişkenleri](cdn-http-variables.md) <br/> <br/> Kaynak düzende $ kullanarak hedef modele yakalanan değerler yerine_n_ burada _n_ bunu yakalanan sıralama ölçütü bir değer tanımlar. Örneğin, 1 USD $2 ikinci değer temsil ederken, kaynak desende yakalanan ilk değeri temsil eder.

 Bu özellik, geleneksel bir yeniden yönlendirme işlemi yapmadan URL yeniden yazma Pop'lere sağlar. Diğer bir deyişle, istek sahibinin yeniden URL istenen gibi aynı yanıt kodu alır.

**Örnek Senaryo 1**

Bu örnek bir kenar temel bu CDN URL'ye çözümler CNAME URL yeniden yönlendirme gösterilmektedir: http:\//marketing.azureedge.net/brochures/

İstekleri uygun bu temel edge CNAME URL yönlendirilirsiniz: http:\//MyOrigin.azureedge.net/resources/

Bu URL yeniden yönlendirmesi aşağıdaki yapılandırma elde edilebilir: ![URL yeniden yönlendirme](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Örnek Senaryo 2**

Bu örnek normal ifadeler kullanarak küçük büyük harf CNAME URL'den kenar yeniden yönlendirileceği gösterilmiştir.

Bu URL yeniden yönlendirmesi aşağıdaki yapılandırma elde edilebilir: ![URL yeniden yönlendirme](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Önemli noktalar:**

- İstek URL yeniden yazma özelliği tanımlar yazılacak URL'leri. Sonuç olarak, ek eşleştirme koşulları gerekli değildir. Eşleşme koşulu "her zaman" olarak tanımlandı ancak "broşürler" klasörü "Pazarlama" Müşteri kaynağı işaret eden istekleri yazılacaktır.

- İstekten yakalanan URL kesimleri "$1." aracılığıyla yeni URL'sine eklenir

#### <a name="compatibility"></a>Uyumluluk

Bu özellik, bir istek uygulanmadan önce karşılanması gereken ölçütlerle eşleşen içerir. Çakışan eşleştirme ölçütü ayarlama önlemek için bu özellik aşağıdaki eşleştirme koşulları ile uyumsuz.:

- Sayı olarak
- CDN kaynağı
- İstemci IP adresi
- Müşteri kaynağı
- İstek düzeni
- URL yol dizini
- URL yolu genişletme
- URL yol dosyaadı
- URL yolu değişmez değeri
- URL yolu normal ifade
- URL yolu joker karakter
- URL sorgu değişmez değeri
- URL sorgu parametresi
- Sorgu Regex URL'si
- URL sorgu joker karakter

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Kullanıcı değişkeni

**Amaç:** Yalnızca iç kullanım içindir.

[Başa dön](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>Sonraki adımlar

- [Kural altyapısı başvurusu](cdn-verizon-premium-rules-engine-reference.md)
- [Kural altyapısı koşullu ifadeleri](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Kural altyapısı eşleştirme koşulları](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Kural altyapısı kullanarak HTTP davranışı geçersiz kılma](cdn-verizon-premium-rules-engine.md)
- [Azure CDN'ye genel bakış](cdn-overview.md)
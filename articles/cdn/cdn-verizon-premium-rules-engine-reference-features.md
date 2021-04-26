---
title: Verizon Premium kural altyapısı özelliklerinden Azure CDN
description: Verizon Premium kural altyapısı özelliklerinden Azure CDN için başvuru belgeleri.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020424"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Verizon Premium kural altyapısı özelliklerinden Azure CDN

Bu makalede, Azure Content Delivery Network (CDN) [kuralları altyapısının](cdn-verizon-premium-rules-engine.md)kullanılabilir özelliklerinin ayrıntılı açıklamaları listelenmektedir.

Kuralın üçüncü bölümü özelliğidir. Bir özellik, bir eşleşme koşulları kümesi tarafından tanımlanan istek türüne uygulanan eylemin türünü tanımlar.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Verizon Premium kural altyapısı Özellikler başvurusundan Azure CDN

Kullanılabilir özellik türleri şunlardır:

* [Erişim](#access)
* [Önbelleğe Alma](#caching)
* [Yorum](#comment)
* [Üst Bilgiler](#headers)
* [Günlükler](#logs)
* [Getirileceğini](#optimize)
* [Tıdır](#origin)
* [Uzmanlık](#specialty)
* [URL](#url)
* [Web Uygulaması Güvenlik Duvarı](#waf)

### <a name="access"></a><a name="access"></a>Access

Bu özellikler, içeriğe erişimi denetlemek için tasarlanmıştır.

| Name       | Amaç                                                           |
|------------|-------------------------------------------------------------------|
| [Erişimi Reddet (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Tüm isteklerin 403 yasaklanmış bir Yanıt ile reddedildiğini belirler. |
| [Belirteç kimlik doğrulaması](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Token-Based kimlik doğrulamasının bir isteğe uygulanıp uygulanmayacağını belirler. |
| [Belirteç kimlik doğrulaması reddi kodu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Token-Based kimlik doğrulaması nedeniyle bir istek reddedildiğinde kullanıcıya döndürülecek yanıtın türünü belirler. |
| [Belirteç kimlik doğrulaması URL 'sini yoksay](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Token-Based kimlik doğrulaması tarafından yapılan URL karşılaştırmalarının büyük/küçük harfe duyarlı olup olmadığını belirler. |
| [Belirteç kimlik doğrulaması parametresi](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Token-Based kimlik doğrulaması sorgu dizesi parametresinin yeniden adlandırılması gerekip gerekmediğini belirler. |

**[En üste geri dön](#top)**

### <a name="caching"></a><a name="caching"></a>Önbelleğe Alma

Bu özellikler, içeriğin ne zaman ve nasıl önbelleğe alınacağını özelleştirmek için tasarlanmıştır.

| Name       | Amaç                                                           |
|------------|-------------------------------------------------------------------|
| [Bant genişliği parametreleri](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Bant genişliği azaltma parametrelerinin (yani, ec_rate ve ec_prebuf) etkin olup olmayacağını belirler. |
| [Bant genişliği azaltma](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Edge sunucularımız tarafından sağlanmış olan yanıtın bant genişliğini kısıtlar. |
| [Atlama önbelleği](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | İsteğin önbelleğe alma teknolojimizden yararlanıp yararlanamayacağını belirler. |
| [Cache-Control üst bilgisi Işleme](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Dış Max-Age özelliği etkinken uç sunucuya Cache-Control üst bilgileri oluşturmayı denetler. |
| [Cache-Key sorgu dizesi](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | **Cache-Key** _ ' ın bir istekle ilişkili sorgu dizesi parametrelerini içerip içermediğini veya hariç tutmasını belirler. <br> Bir varlığı, önbelleğe alma amacına göre benzersiz bir şekilde tanımlayan göreli bir yol.  Uç sunucularımız, önbelleğe alınmış içerik denetlenirken bu göreli yolu kullanır.  Varsayılan olarak, bir önbellek anahtarı sorgu dizesi içermez parameters._ |
| [Cache-Key yeniden yazma](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | Bir istekle ilişkili önbellek anahtarını yeniden yazar. |
| [Önbellek dolgusunu tamamla](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Bir uç sunucusunda bir istek kısmi önbellek isabetsizliği ile sonuçlanırsa ne olacağını belirler. |
| [Dosya türlerini sıkıştır](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Sunucuda Sıkıştırılacak dosya biçimlerini tanımlar. | 
| [Varsayılan dahili maksimum yaş](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Uç sunucusunun kaynak sunucu önbelleği yeniden doğrulama için varsayılan en yüksek yaş aralığını belirler. |
| [Süre sonu üst bilgisi Işleme](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Dış Max-Age özelliği etkin olduğunda bir uç sunucu tarafından süre sonu başlıklarının oluşturulmasını denetler. |
| [Dış maksimum yaş](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Tarayıcı için uç sunucu önbelleği yeniden doğrulama için maksimum yaş aralığını belirler. |
| [Maksimum Iç kullanım süresi](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Uç sunucusu için sunucu önbelleği yeniden doğrulama için maksimum yaş aralığını belirler. |
| [H. ula desteği (HTTP aşamalı Indirme)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | İçerik akışı için kullanılabilen H., dosya biçimlerinin türlerini belirler. |
| [H. Bu, video arama params desteği](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | HTTP aşamalı Indirme kullanılırken H. bir medyada aramayı denetleyen parametrelere atanan adları geçersiz kılar. |
| [No-Cache Isteği dikkate alarak](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | HTTP istemcisinin önbelleğe alma isteklerinin kaynak sunucuya iletilip iletilmeyeceğini belirler. |
| [Önbellek yok sayma](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | CDN 'nin bir kaynak sunucudan sunulan belirli yönergeleri yoksayıp saymayacağını belirler. |
| [Unsatisfiable aralıklarını yoksay](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | İstek bir 416 Istenen Aralık Satisfiable durum kodu oluşturduğunda istemcilere döndürülecek yanıtı belirler. |
| [İç maksimum-eski](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Uç sunucu, önbelleğe alınmış varlığı kaynak sunucu ile yeniden doğrulayamamışsa, son tarihi geçmiş bir varlık bir uç sunucudan ne kadar süre sonra hizmet verilebileceğini denetler. |
| [Kısmi önbellek paylaşımı](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Bir isteğin kısmen önbelleğe alınmış içerik oluşturup üretmeyeceğini belirler. |
| [Önbelleğe alınmış Içeriği önceden doğrula](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Önbelleğe alınan içeriğin TTL 'nin süresi dolmadan erken yeniden doğrulama için uygun olup olmayacağını belirler. |
| [Zero-Byte önbellek dosyalarını Yenile](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Bir HTTP istemcisinin 0 baytlık önbellek varlığı için olan isteğinin Edge sunucularımız tarafından nasıl işlendiğini belirler. |
| [Önbelleklenebilir durum kodlarını ayarlama](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Önbelleğe alınmış içerikle sonuçlanabilme durum kodları kümesini tanımlar. |
| [Hatada eski Içerik teslimi](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Önbellek yeniden doğrulama işlemi sırasında veya müşterinin kaynak sunucusundan istenen içerik alınırken bir hata oluştuğunda, süre dolma önbelleğe alınmış içeriğin teslim edilip edilmeyeceğini belirler. | 
| [Yeniden doğrulama eski](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | , Uç sunucularımızın eski istemciyi istek sahibine yeniden doğrulama gerçekleştiğinde sunmalarına izin vererek performansı geliştirir. |

**[En üste geri dön](#top)**

### <a name="comment"></a><a name="comment"></a>Yorum

Açıklama özelliği, bir notun bir kural içine eklenmesine izin verir.

**[En üste geri dön](#top)**

### <a name="headers"></a><a name="headers"></a>Üst Bilgiler

Bu özellikler, istek veya yanıta üstbilgiler eklemek, değiştirmek veya silmek için tasarlanmıştır.

| Name       | Amaç                                                           |
|------------|-------------------------------------------------------------------|
| [Yaş yanıt üst bilgisi](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | İstek sahibine gönderilen yanıta bir yaş yanıtı üstbilgisinin eklenip eklenmeyeceğini belirler. |
| [Önbellek yanıtı üst bilgilerinde hata ayıkla](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Bir yanıtın, istenen varlık için önbellek ilkesi hakkında bilgi sağlayan [X-EC-Debug yanıt üst bilgisini](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) içerip içeremeyeceğini belirler. |
| [Istemci Isteği üst bilgisini Değiştir](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Bir istekten üst bilgi üzerine yazar, ekler veya siler. |
| [Istemci yanıtı başlığını Değiştir](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Bir yanıttan üst bilgi üzerine yazar, ekler veya siler. |
| [Istemci IP özel üstbilgisini ayarla](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | İstekte bulunan istemcinin IP adresinin isteğe özel bir istek üst bilgisi olarak eklenmesine izin verir. |

**[En üste geri dön](#top)**

### <a name="logs"></a><a name="logs"></a>Günlükler

Bu özellikler ham günlük dosyalarında depolanan verileri özelleştirmek için tasarlanmıştır.

| Name       | Amaç                                                           |
|------------|-------------------------------------------------------------------|
| [Özel günlük alanı 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Ham günlük dosyasındaki özel günlük alanına atanacak biçimi ve içeriği belirler. |
| [Günlük sorgu dizesi](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Sorgu dizesinin, erişim günlüklerindeki URL ile birlikte saklanıp saklanmayacağını belirler. |

**[En üste geri dön](#top)**

### <a name="optimize"></a><a name="optimize"></a>Getirileceğini

Bu özellikler, bir isteğin Edge Idealleştirici tarafından sağlanan iyileştirmelere sahip olup olmayacağını tespit eder.

| Name       | Amaç                                                           |
|------------|-------------------------------------------------------------------|
| [Edge Idealleştirici](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Bir isteğe kenar Iyileştiricinin uygulanıp uygulanamayacağını belirler. |
| [Edge Idealleştirici – yapılandırma oluştur](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Bir siteyle ilişkili Edge Idealleştirici yapılandırmasını başlatır veya etkinleştirir. |

**[En üste geri dön](#top)**

### <a name="origin"></a><a name="origin"></a>Tıdır

Bu özellikler, CDN 'nin bir kaynak sunucu ile nasıl iletişim kuracağını denetlemek için tasarlanmıştır.

| Name       | Amaç                                                           |
|------------|-------------------------------------------------------------------|
| [En fazla Keep-Alive Isteği](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Kapatılmadan önce Keep-Alive bağlantı için en fazla istek sayısını tanımlar. |
| [Proxy özel üstbilgileri](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Bir uç sunucudan bir kaynak sunucuya iletilecek olan [CDN 'e özgü istek üst bilgileri](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) kümesini tanımlar. |

**[En üste geri dön](#top)**

### <a name="specialty"></a><a name="specialty"></a>Uzmanlık

Bu özellikler, yalnızca gelişmiş kullanıcılar tarafından kullanılması gereken gelişmiş işlevler sağlar.

| Name       | Amaç                                                           |
|------------|-------------------------------------------------------------------|
| [Önbelleklenebilir HTTP yöntemleri](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Ağımızda Önbelleğe alınabilecek ek HTTP yöntemleri kümesini belirler. |
| [Önbelleklenebilir Istek gövdesi boyutu](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Bir POST yanıtının önbelleğe alınıp alınmayacağını belirlemek için eşiği tanımlar. |
| [QUıC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | CDN hizmetimizin QUIC 'yi destekleyip desteklemediğini istemciye karşı haberdar olup olmayacağını belirler. |
| [Akış Iyileştirmesi](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Canlı akışlar için performansı iyileştirmek ve kaynak sunucudaki yükü azaltmak için önbelleğe alma yapılandırmanızı Tunus. |
| [Kullanıcı değişkeni](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Kullanıcı tanımlı bir değişkene Bespoke trafik işleme çözümünüze geçirilen bir değer atar. |

**[En üste geri dön](#top)**

### <a name="url"></a><a name="url"></a>URL

Bu özellikler, bir isteğin farklı bir URL 'ye yönlendirilmesini veya yeniden yönlendirilmesine izin verir.

| Name       | Amaç                                                           |
|------------|-------------------------------------------------------------------|
| [Yeniden yönlendirmeleri takip et](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | İsteklerin bir müşteri kaynak sunucusu tarafından döndürülen konum üstbilgisinde tanımlanan ana bilgisayar adına yönlendirilip yönlendirilmeyeceğini belirler. |
| [URL yeniden yönlendirme](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | İstekleri konum üst bilgisi aracılığıyla yeniden yönlendirir. |
| [URL yeniden yazma](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | İstek URL 'sini yeniden yazar. |

**[En üste geri dön](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Web Uygulaması Güvenlik Duvarı

[Web uygulaması güvenlik duvarı](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) özelliği, bir Isteğin Web uygulaması güvenlik duvarı tarafından görüntülenip görüntülenmeyeceğini belirler.

**[En üste geri dön](#top)**

En son özellikler için [Verizon Rules Engine belgelerine](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions)bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Kural altyapısı başvurusu](cdn-verizon-premium-rules-engine-reference.md)
- [Kural altyapısı koşullu ifadeleri](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Kural altyapısı eşleştirme koşulları](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Kural altyapısını kullanarak HTTP davranışını geçersiz kılma](cdn-verizon-premium-rules-engine.md)
- [Azure CDN genel bakış](cdn-overview.md)

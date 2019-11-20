---
title: Verizon Premium kural altyapısından eşleşme koşullarına Azure CDN | Microsoft Docs
description: Verizon Premium kuralları altyapısı eşleştirme koşullarından Azure Content Delivery Network için başvuru belgeleri.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593211"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Verizon Premium kural altyapısından eşleşme koşullarına Azure CDN

Bu makalede, Verizon Premium [kural altyapısından](cdn-verizon-premium-rules-engine.md)Azure Content DELIVERY Network (CDN) için kullanılabilen eşleşme koşullarının ayrıntılı açıklamaları listelenmektedir.

Bir kuralın ikinci bölümü eşleşme durumudur. Bir eşleşme koşulu, bir özellik kümesinin gerçekleştirileceği belirli istek türlerini tanımlar.

Örneğin, bir eşleşme koşulunu şu şekilde kullanabilirsiniz:

- Belirli bir konumdaki içerik isteklerini filtreleyin.
- Belirli bir IP adresi veya ülke/bölge tarafından oluşturulan filtre istekleri.
- İstekleri üst bilgi bilgisine göre filtreleyin.

## <a name="always-match-condition"></a>Her zaman eşleşen koşul

Her zaman eşleşme koşulu, tüm isteklere varsayılan bir özellik kümesi uygular.

Ad | Amaç
-----|--------
[Her zaman](#always) | Tüm isteklere varsayılan bir özellik kümesi uygular.

## <a name="device-match-condition"></a>Cihaz eşleştirme koşulu

Cihaz eşleştirme koşulu, bir mobil cihazdan yapılan istekleri özelliklerine göre tanımlar.  

Ad | Amaç
-----|--------
[cihaz](#device) | Bir mobil cihazdan yapılan istekleri özelliklerine göre tanımlar.

## <a name="location-match-conditions"></a>Konum eşleşme koşulları

Konum eşleşme koşulları, istek sahibinin konumuna göre istekleri belirler.

Ad | Amaç
-----|--------
[AS numarası](#as-number) | Belirli bir ağdan kaynaklanan istekleri tanımlar.
[Ülke](#country) | Belirtilen ülkelerden/bölgelerden kaynaklanan istekleri tanımlar.

## <a name="origin-match-conditions"></a>Kaynak eşleşme koşulları

Kaynak eşleşme koşulları Content Delivery Network depolama alanını veya bir müşteri kaynak sunucusunu işaret eden istekleri belirler.

Ad | Amaç
-----|--------
[CDN kaynağı](#cdn-origin) | Content Delivery Network depolamada depolanan içerik için istekleri tanımlar.
[Müşteri kaynağı](#customer-origin) | Belirli bir müşteri kaynağı sunucusunda depolanan içerik isteklerini tanımlar.

## <a name="request-match-conditions"></a>İstek eşleştirme koşulları

Istek eşleştirme koşulları, isteklerini özelliklerine göre belirler.

Ad | Amaç
-----|--------
[İstemci IP adresi](#client-ip-address) | Belirli bir IP adresinden kaynaklanan istekleri tanımlar.
[Cookie parametresi](#cookie-parameter) | Belirtilen değer için her istekle ilişkili tanımlama bilgilerini denetler.
[Tanımlama bilgisi parametre Regex](#cookie-parameter-regex) | Belirtilen normal ifade için her istekle ilişkili tanımlama bilgilerini denetler.
[Edge CNAME](#edge-cname) | Belirli bir Edge CNAME 'e işaret eden istekleri tanımlar.
[Başvurulan etki alanı](#referring-domain) | Belirtilen ana bilgisayar adlarından başvurulan istekleri tanımlar.
[İstek üst bilgisi sabit değeri](#request-header-literal) | Belirtilen üst bilgiyi içeren istekleri belirtilen bir değere göre tanımlar.
[İstek üst bilgisi Regex](#request-header-regex) | Belirtilen üst bilgiyi içeren istekleri belirtilen normal ifadeyle eşleşen bir değere tanımlar.
[İstek üstbilgisi joker karakteri](#request-header-wildcard) | Belirtilen üst bilgiyi içeren istekleri belirtilen Düzenle eşleşen bir değere belirler.
[İstek yöntemi](#request-method) | İstekleri HTTP yöntemine göre tanımlar.
[İstek düzeni](#request-scheme) | İstekleri HTTP protokollerine göre tanımlar.

## <a name="url-match-conditions"></a>URL eşleşme koşulları

URL eşleştirme koşulları, isteklerini URL 'lerine göre belirler.

Ad | Amaç
-----|--------
[URL yolu dizini](#url-path-directory) | İstekleri göreli yoluna göre tanımlar.
[URL yolu uzantısı](#url-path-extension) | İstekleri dosya adı uzantısına göre tanımlar.
[URL yolu dosya adı](#url-path-filename) | İstekleri dosya adlarıyla tanımlar.
[URL yolu sabit değeri](#url-path-literal) | Bir isteğin göreli yolunu belirtilen değerle karşılaştırır.
[URL yolu Regex](#url-path-regex) | Bir isteğin göreli yolunu belirtilen normal ifadeyle karşılaştırır.
[URL yolu joker karakteri](#url-path-wildcard) | Bir isteğin göreli yolunu belirtilen düzene göre karşılaştırır.
[URL sorgusu sabit değeri](#url-query-literal) | Bir isteğin sorgu dizesini belirtilen değerle karşılaştırır.
[URL sorgu parametresi](#url-query-parameter) | Belirtilen bir düzenle eşleşen bir değere ayarlanan belirtilen sorgu dizesi parametresini içeren istekleri tanımlar.
[URL sorgu Regex](#url-query-regex) | Belirtilen sorgu dizesi parametresini içeren istekleri belirtilen normal ifadeyle eşleşen bir değere ayarlar.
[URL sorgusu joker karakteri](#url-query-wildcard) | Belirtilen değeri isteğin sorgu dizesiyle karşılaştırır.

## <a name="reference-for-rules-engine-match-conditions"></a>Kural altyapısı eşleştirme koşulları başvurusu

---

### <a name="always"></a>Her zaman

Her zaman eşleşme koşulu, tüm isteklere varsayılan bir özellik kümesi uygular.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS numarası

AS numarası ağı, otonom sistem numarası (ASN) tarafından tanımlanır. 

**Eşleşmeler**/**eşleşmiyor** seçeneği, as numarası eşleşme koşulunun karşılandığı koşulları belirler:

- **Eşleşmeler**: İstemci ağının ASN 'nin belirtilen ASNs 'den biriyle eşleşmesini gerektirir. 
- **Eşleşmiyor**: İstemci ağının ASN 'nin belirtilen ASNs ile eşleşmemesi gerekir.

Anahtar bilgileri:

- Her birini tek bir boşlukla ayırarak birden çok ASNs belirtin. Örneğin 64514 64515, 64514 veya 64515 ' den gelen isteklerle eşleşir.
- Belirli istekler geçerli bir ASN döndürmeyebilir. Bir soru işareti (?), geçerli bir ASN 'nin belirlenemediği isteklerle eşleşir.
- İstediğiniz ağ için tüm ASN 'yi belirtin. Kısmi değerler eşleştirmeyecektir.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN kaynağı

Aşağıdaki koşulların her ikisi de karşılandığında CDN kaynak eşleşmesi koşulu karşılanır:

- CDN depolamadaki içerik istendi.
- İstek URI 'SI, bu eşleştirme koşulunda tanımlanmış içerik erişim noktası türünü (örneğin,/000001) kullanır:
  - CDN URL 'SI: İstek URI 'SI seçilen içerik erişim noktasını içermelidir.
  - Edge CNAME URL 'SI: Karşılık gelen Edge CNAME yapılandırması, seçilen içerik erişim noktasına işaret etmelidir.
  
Anahtar bilgileri:

- İçerik erişim noktası, istenen içeriğe hizmet vermesi gereken hizmeti belirler.
- Belirli eşleşme koşullarını birleştirmek için ve IF ifadesini kullanmayın. Örneğin, bir müşteri kaynağı eşleştirme koşuluna sahip bir CDN kaynak eşleşmesi koşulunu birleştirmek, hiçbir şekilde eşleştirilememe bir eşleştirme deseninin oluşturulmasını sağlayabilir. Bu nedenle, iki CDN kaynağı eşleşme koşulu bir ve IF ifadesiyle birleştirilemez.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>İstemci IP Adresi

**Eşleşmeler**/**eşleşmiyor** seçeneği, istemci IP adresi eşleşme koşulunun karşılandığı koşulları belirler:

- **Eşleşmeler**: İstemcinin IP adresinin belirtilen IP adreslerinden biriyle eşleşmesini gerektirir. 
- **Eşleşmiyor**: İstemcinin IP adresinin belirtilen IP adreslerinden hiçbiriyle eşleşmemesi gerekir. 

Anahtar bilgileri:

- CıDR gösterimini kullanın.
- Her birini tek bir boşlukla ayırarak birden çok IP adresi ve/veya IP adresi bloğu belirtin. Örneğin:
  - **IPv4 örneği**: 1.2.3.4 10.20.30.40, 1.2.3.4 veya 10.20.30.40 adresinden gelen isteklerle eşleşir.
  - **IPv6 örneği**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80, 1:2:3:4:5:6:7:8 veya 10:20:30:40:50:60:70:80 adresinden gelen isteklerle eşleşir.
- Bir IP adres bloğunun sözdizimi, ardından bir eğik çizgi ve ön ek boyutu gelen temel IP adresidir. Örneğin:
  - **IPv4 örneği**: 5.5.5.64/26, 5.5.5.64 ile 5.5.5.127 arası adreslerden gelen isteklerle eşleşir.
  - **IPv6 örneği**: 1:2:3:/48, 1:2:3:0:0:0:0:0 ila 1:2: 3: ffff: ffff: ffff: ffff: ffff: ffff adresinden gelen isteklerle eşleşir.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie parametresi

**Eşleşmeler**/**eşleşmiyor** seçeneği, tanımlama bilgisi parametresi eşleşme koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: Bu eşleşme koşulunda tanımlanan değerlerden en az biriyle eşleşen bir değere sahip belirtilen tanımlama bilgisini içermesi için bir istek gerektirir.
- **Eşleşmiyor**: İsteğin aşağıdaki ölçütlerden birini karşılaması gerekir:
  - Belirtilen tanımlama bilgisini içermez.
  - Belirtilen tanımlama bilgisini içerir, ancak değeri bu eşleşme koşulunda tanımlanan değerlerden hiçbiriyle eşleşmiyor.
  
Anahtar bilgileri:

- Tanımlama bilgisi adı:
  - Bir tanımlama bilgisi adı belirtirken yıldız işareti (*) de dahil olmak üzere joker karakter değerleri desteklenmediğinden, yalnızca tam tanımlama bilgisi adı karşılaştırma için uygundur.
  - Bu eşleşme koşulunun örneği başına yalnızca tek bir tanımlama bilgisi adı belirtilebilir.
  - Tanımlama bilgisi adı karşılaştırmaları büyük/küçük harfe duyarlıdır.
- Tanımlama bilgisi değeri:
  - Her birini tek bir boşlukla ayırarak birden fazla tanımlama bilgisi değeri belirtin.
  - Bir tanımlama bilgisi değeri, [Joker değerlerden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)yararlanabilir.
  - Joker karakter değeri belirtilmediyse, bu eşleştirme koşulunu yalnızca tam bir eşleşme karşılar. Örneğin, "Value" belirtildiğinde "Value" ile eşleşir, ancak "değer1" veya "değer2" olarak eşleşmez.
  - İsteğin tanımlama bilgisine göre büyük/küçük harfe duyarlı bir karşılaştırma yapılıp yapılmadığını denetlemek için **durumu yoksay** seçeneğini kullanın.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Tanımlama bilgisi parametre Regex

Tanımlama bilgisi parametresi Regex Match koşulu, bir tanımlama bilgisi adı ve değeri tanımlar. İstenen tanımlama bilgisi değerini tanımlamak için [Normal ifadeleri](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) kullanabilirsiniz.

**Eşleşmeler**/**eşleşmiyor** seçeneğiyle eşleşen tanımlama bilgisi parametresinin hangi koşullarda karşılandığını belirler.

- **Eşleşmeler**: Belirtilen normal ifadeyle eşleşen bir değere sahip belirtilen tanımlama bilgisini içermesi için bir istek gerektirir.
- **Eşleşmiyor**: İsteğin aşağıdaki ölçütlerden birini karşılaması gerekir:
  - Belirtilen tanımlama bilgisini içermez.
  - Belirtilen tanımlama bilgisini içerir, ancak değeri belirtilen normal ifadeyle eşleşmez.
  
Anahtar bilgileri:

- Tanımlama bilgisi adı:
  - Bir tanımlama bilgisi adı belirtirken normal ifadeler ve yıldız işareti (*) de dahil olmak üzere joker değerler desteklenmediğinden, yalnızca tam tanımlama bilgisi adı karşılaştırma için uygundur.
  - Bu eşleşme koşulunun örneği başına yalnızca tek bir tanımlama bilgisi adı belirtilebilir.
  - Tanımlama bilgisi adı karşılaştırmaları büyük/küçük harfe duyarlıdır.
- Tanımlama bilgisi değeri:
  - Bir tanımlama bilgisi değeri, normal ifadelerden yararlanabilir.
  - İsteğin tanımlama bilgisine göre büyük/küçük harfe duyarlı bir karşılaştırma yapılıp yapılmadığını denetlemek için **durumu yoksay** seçeneğini kullanın.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

Ülke kodu aracılığıyla bir ülke belirtebilirsiniz. 

**Eşleşmeler**/**eşleşmiyor** seçeneği, ülke eşleşme koşulunun karşılandığı koşulları belirler:

- **Eşleşmeler**: İsteğin belirtilen ülke kodu değerlerini içermesini gerektirir. 
- **Eşleşmiyor**: İsteğin belirtilen ülke kodu değerlerini içermemesi gerekir.

Anahtar bilgileri:

- Her birini tek bir boşlukla ayırarak birden çok ülke kodu belirtin.
- Ülke kodu belirtirken joker karakterler desteklenmez.
- "AB" ve "AP" ülke kodları, bu bölgelerdeki tüm IP adreslerini içermez.
- Belirli istekler geçerli bir ülke kodu döndürmeyebilir. Bir soru işareti (?), geçerli bir ülke kodunun belirlenemediği isteklerle eşleşir.
- Ülke kodları büyük/küçük harfe duyarlıdır.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Kural altyapısını kullanarak ülke filtreleme uygulama

Bu eşleştirme koşulu, bir isteğin kaynaklandığı konuma göre çok sayıda özelleştirmeler gerçekleştirmenize olanak tanır. Örneğin, ülke filtreleme özelliğinin davranışı aşağıdaki yapılandırma yoluyla çoğaltılabiliyor:

- URL yolu joker karakter eşleşmesi: [URL yolu joker karakter eşleştirme koşulunu](#url-path-wildcard) güvenliği sağlayacak dizine ayarlayın. 
    Tüm alt öğelerine erişimin bu kural tarafından kısıtlanmasını sağlamak için göreli yolun sonuna bir yıldız işareti ekleyin.

- Ülke eşleşmesi: Ülke eşleştirme koşulunu istenen ülke kümesine ayarlayın.
  - Açılmasına Ülke eşleştirme koşulunu, yalnızca belirtilen ülkelerin, URL yolu eşleştirme koşulu tarafından tanımlanan konumda depolanan içeriğe erişmesine izin verecek şekilde **eşleşmez** olarak ayarlayın.
  - Engelleyin Belirtilen ülkelerin, URL yolu joker karakter eşleştirme koşulu tarafından tanımlanan konumda depolanan içeriğe erişmesini engellemek için ülke eşleştirme koşulunu **eşleşir** olarak ayarlayın.

- Erişimi Reddet (403) özelliği: [Erişim reddetme (403) özelliğini](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) , ülke filtreleme özelliğinin izin ver veya engelle bölümünü çoğaltmak üzere etkinleştirin.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Müşteri kaynağı

Anahtar bilgileri:

- Müşteri kaynağı eşleştirme koşulu, içeriğin bir CDN URL 'SI veya seçili müşteri kaynağına işaret eden bir uç CNAME URL 'SI aracılığıyla istenip istenmediğine bakılmaksızın karşılanır.
- Bir kural tarafından başvurulan bir müşteri kaynağı yapılandırması, müşteri kaynağı sayfasından silinemez. Müşteri kaynak yapılandırmasını silmeyi denemeden önce, aşağıdaki yapılandırmaların bu yapılandırmaya başvurmadığından emin olun:
  - Müşteri kaynağı eşleşme koşulu
  - Bir Edge CNAME yapılandırması
- Belirli eşleşme koşullarını birleştirmek için ve IF ifadesini kullanmayın. Örneğin, bir müşteri kaynağı eşleşme koşulunu CDN kaynak eşleşmesi durumuyla birleştirmek, hiçbir şekilde eşleştirilememe bir eşleştirme deseninin oluşturulmasını sağlayabilir. Bu nedenle, iki müşteri kökeni eşleşme koşulu bir ve If ifadesiyle birleştirilemez.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Cihaz

Cihaz eşleştirme koşulu, bir mobil cihazdan yapılan istekleri özelliklerine göre tanımlar. Mobil cihaz algılama, [WURFL](http://wurfl.sourceforge.net/)üzerinden sağlanır. 

**Eşleşmeler**/**eşleşmiyor** seçeneği, cihazın eşleşme koşulunun karşılandığı koşulları belirler:

- **Eşleşmeler**: İstek sahibinin cihazının belirtilen değerle eşleşmesini gerektirir. 
- **Eşleşmiyor**: İstek sahibinin cihazının belirtilen değerle eşleşmemesi gerekir.

Anahtar bilgileri:

- Belirtilen değerin büyük/küçük harfe duyarlı olup olmadığını belirtmek için **durum yoksay** seçeneğini kullanın.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

#### <a name="string-type"></a>Dize türü

Bir WURFL özelliği genellikle sayıların, harflerin ve simgelerin birleşimini kabul eder. Bu özelliğin esnek yapısı nedeniyle, bu eşleştirme durumuyla ilişkili değerin nasıl yorumlandığını seçmeniz gerekir. Aşağıdaki tabloda kullanılabilir seçenekler kümesi açıklanmaktadır:

Type     | Açıklama
---------|------------
Ayarını  | Çoğu karakterin kendi [sabit değerlerini](cdn-verizon-premium-rules-engine-reference.md#literal-values)kullanarak özel anlam almasını engellemek için bu seçeneği belirleyin.
Liyorsa | Tüm [joker karakter] ([Joker değerlerden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)) yararlanmak için bu seçeneği belirleyin.
Regex    | [Normal ifadeleri](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)kullanmak için bu seçeneği belirleyin. Normal ifadeler, bir karakter deseninin tanımlanması için faydalıdır.

#### <a name="wurfl-capabilities"></a>WURFL özellikleri

WURFL özelliği, mobil cihazları açıklayan bir kategoriye başvurur. Seçilen yetenek, istekleri tanımlamak için kullanılan mobil cihaz açıklaması türünü belirler.

Aşağıdaki tabloda Rules altyapısının WURFL özellikleri ve değişkenleri listelenmektedir.

> [!NOTE]
> Aşağıdaki değişkenler **Istemci Isteği üst bilgisini Değiştir** ve **Istemci yanıtı üst bilgisi özelliklerini değiştir** ' de desteklenir.

Özellik | Değişken | Açıklama | Örnek değerler
-----------|----------|-------------|----------------
Marka adı | %{wurfl_cap_brand_name} | Cihazın marka adını gösteren bir dize. | Samsung
Cihaz işletim sistemi | %{wurfl_cap_device_os} | Cihaza yüklü işletim sistemini gösteren bir dize. | IOS
Cihaz İşletim Sistemi Sürümü | %{wurfl_cap_device_os_version} | Cihazda yüklü olan işletim sisteminin sürüm numarasını gösteren bir dize. | 1.0.1
Çift Yön | %{wurfl_cap_dual_orientation} | Cihazın çift yönlü yönlendirmeyi destekleyip desteklemediğini gösteren bir Boole değeri. | true
HTML tercih edilen DTD | %{wurfl_cap_html_preferred_dtd} | HTML içeriği için mobil cihazın tercih edilen belge türü tanımını (DTD) gösteren bir dize. | yok<br/>xhtml_basic<br/>HTML5
Görüntü satır içi | %{wurfl_cap_image_inlining} | Cihazın Base64 kodlamalı görüntüleri destekleyip desteklemediğini gösteren bir Boole değeri. | false
Android | %{wurfl_vcap_is_android} | Cihazın Android işletim sistemini kullanıp kullanmadığını gösteren bir Boole değeri. | true
IOS | %{wurfl_vcap_is_ios} | Cihazın iOS kullanıp kullanmadığını gösteren bir Boole değeri. | false
Akıllı TV | %{wurfl_cap_is_smarttv} | Cihazın akıllı bir TV olup olmadığını gösteren bir Boole değeri. | false
Smartphone | %{wurfl_vcap_is_smartphone} | Cihazın bir akıllı telefon olup olmadığını gösteren bir Boole değeri. | true
Tabletdir | %{wurfl_cap_is_tablet} | Cihazın bir tablet olup olmadığını gösteren bir Boole değeri. Bu açıklama işletim sistemine bağımsızdır. | true
Kablosuz cihaz | %{wurfl_cap_is_wireless_device} | Cihazın kablosuz bir cihaz olarak kabul edilip edilmeyeceğini belirten bir Boole değeri. | true
Pazarlama adı | %{wurfl_cap_marketing_name} | Cihazın pazarlama adını belirten bir dize. | BlackBerry 8100 armut
Mobil tarayıcı | %{wurfl_cap_mobile_browser} | Cihazdan içerik istemek için kullanılan tarayıcıyı gösteren bir dize. | Chrome
Mobil tarayıcı sürümü | %{wurfl_cap_mobile_browser_version} | Cihazdan içerik istemek için kullanılan tarayıcının sürümünü belirten bir dize. | 31
Model Adı | %{wurfl_cap_model_name} | Cihazın model adını gösteren bir dize. | bekletmeden
Aşamalı Indirme | %{wurfl_cap_progressive_download} | Cihazın, hala indirilmekte olan ses ve video kayıttan yürütmesini destekleyip desteklemediğini belirten bir Boole değeri. | true
Yayınlanma Tarihi | %{wurfl_cap_release_date} | Cihazın WURFL veritabanına eklendiği yıl ve ayı belirten bir dize.<br/><br/>Biçim: `yyyy_mm` | 2013_december
Çözünürlük yüksekliği | %{wurfl_cap_resolution_height} | Aygıtın yüksekliğini piksel cinsinden belirten bir tamsayı. | 768
Çözünürlük genişliği | %{wurfl_cap_resolution_width} | Aygıtın genişliğini piksel cinsinden belirten bir tamsayı. | 1024

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Edge CNAME

Anahtar bilgileri:

- Kullanılabilir Edge CNAMEs listesi, kural altyapısının yapılandırıldığı platformun Edge CNAMEs sayfasında yapılandırılmış olan Edge CNAMEs 'Ler ile sınırlıdır.
- Bir Edge CNAME yapılandırmasını silmeyi denemeden önce, bir Edge CNAME eşleşme koşulunun buna başvurmadığından emin olun. Bir kuralda tanımlanmış olan Edge CNAME yapılandırması Edge CNAMEs sayfasından silinemez.
- Belirli eşleşme koşullarını birleştirmek için ve IF ifadesini kullanmayın. Örneğin, bir Edge CNAME eşleşme koşulunu müşteri kaynağı eşleştirme durumuyla birleştirmek, hiçbir şekilde eşleştirilememe bir eşleştirme deseninin oluşturulmasını sağlayabilir. Bu nedenle, iki Edge CNAME eşleşme koşulları bir AND IF ifadesiyle birleştirilemez.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Başvurulan etki alanı

İçeriğin istendiği başvuran ile ilişkili ana bilgisayar adı, başvurulan etki alanı koşulunun karşılanıp karşılanmadığını belirler.

**Eşleşmeler**/**eşleşmiyor** seçeneği, başvurulan etki alanı eşleştirme koşulunun karşılandığı koşulları belirler:

- **Eşleşmeler**: Başvurulan ana bilgisayar adının belirtilen değerlerle eşleşmesini gerektirir. 
- **Eşleşmiyor**: Başvurulan ana bilgisayar adının belirtilen değerle eşleşmemesi gerekir.

Anahtar bilgileri:

- Her birini tek bir boşlukla ayırarak birden çok konak adı belirtin.
- Bu eşleştirme koşulu [Joker değerleri](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)destekler.
- Belirtilen değer bir yıldız işareti içermiyorsa, bu değerin, başvuran ana bilgisayar adı için tam bir eşleşme olması gerekir. Örneğin, "mydomain.com" belirtildiğinde "www.mydomain.com" ile eşleşmez.
- Büyük/küçük harfe duyarlı bir karşılaştırmanın yapılıp yapılmadığını denetlemek için **durumu yoksay** seçeneğini kullanın.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>İstek üst bilgisi sabit değeri

**Eşleşmeler**/**eşleşmiyor** seçeneği, istek üst bilgisi değişmez değer eşleşmesi koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin belirtilen üstbilgiyi içermesini gerektirir. Değeri, bu eşleşme koşulunda tanımlananla aynı olmalıdır.
- **Eşleşmiyor**: İsteğin aşağıdaki ölçütlerden birini karşılaması gerekir:
  - Belirtilen üstbilgiyi içermez.
  - Belirtilen üstbilgiyi içeriyor, ancak değeri bu eşleşme koşulunda tanımlı bir değerle eşleşmiyor.
  
Anahtar bilgileri:

- Üst bilgi adı karşılaştırmaları her zaman büyük/küçük harfe duyarlıdır. Üstbilgi değeri karşılaştırmalarının büyük/küçük harf duyarlılığını denetlemek için **durumu yoksay** seçeneğini kullanın.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>İstek üst bilgisi Regex

**Eşleşmeler**/**eşleşmiyor** seçeneği, istek üstbilgisi Regex ile eşleşen koşulun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin belirtilen üstbilgiyi içermesini gerektirir. Değeri, belirtilen [normal ifadede](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)tanımlanan Düzenle eşleşmelidir.
- **Eşleşmiyor**: İsteğin aşağıdaki ölçütlerden birini karşılaması gerekir:
  - Belirtilen üstbilgiyi içermez.
  - Belirtilen üstbilgiyi içeriyor, ancak değeri belirtilen normal ifadeyle eşleşmiyor.

Anahtar bilgileri:

- Üst bilgi adı:
  - Üstbilgi adı karşılaştırmaları büyük/küçük harfe duyarlıdır.
  - Üstbilgi adındaki boşlukları "% 20" ile değiştirin.
- Üst bilgi değeri:
  - Üst bilgi değeri, normal ifadelerden yararlanabilir.
  - Üstbilgi değeri karşılaştırmalarının büyük/küçük harf duyarlılığını denetlemek için **durumu yoksay** seçeneğini kullanın.
  - Eşleşme koşulu yalnızca bir üst bilgi değeri belirtilen desenlerden en az biriyle tam olarak eşleştiğinde karşılanır.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>İstek üstbilgisi joker karakteri

**Eşleşmeler**/**eşleşmiyor** seçeneği, istek üst bilgisi eşleştirme koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin belirtilen üstbilgiyi içermesini gerektirir. Değeri, bu eşleşme koşulunda tanımlanan değerlerden en az biri ile eşleşmelidir.
- **Eşleşmiyor**: İsteğin aşağıdaki ölçütlerden birini karşılaması gerekir:
  - Belirtilen üstbilgiyi içermez.
  - Belirtilen üstbilgiyi içeriyor, ancak değeri belirtilen değerlerden hiçbiriyle eşleşmiyor.
  
Anahtar bilgileri:

- Üst bilgi adı:
  - Üstbilgi adı karşılaştırmaları büyük/küçük harfe duyarlıdır.
  - Üstbilgi adındaki boşluklar "% 20" ile değiştirilmelidir. Bu değeri bir üst bilgi değerinde boşluk belirtmek için de kullanabilirsiniz.
- Üst bilgi değeri:
  - Üst bilgi değeri, [Joker değerlerden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)yararlanabilir.
  - Üstbilgi değeri karşılaştırmalarının büyük/küçük harf duyarlılığını denetlemek için **durumu yoksay** seçeneğini kullanın.
  - Bir üst bilgi değeri belirtilen desenlerden en az biriyle tam olarak eşleştiğinde, bu eşleşme koşulu karşılanır.
  - Her birini tek bir boşlukla ayırarak birden çok değer belirtin.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>İstek yöntemi

Istek yöntemi eşleştirme koşulu yalnızca, varlıklar seçili istek yöntemiyle istendiğinde karşılanır. Kullanılabilir istek yöntemleri şunlardır:

- GET
- HEAD
- POST
- SEÇENEKLER
- PUT
- DELETE
- TRACE
- BAĞLANABILMENIZ

Anahtar bilgileri:

- Varsayılan olarak, yalnızca GET isteği yöntemi ağ üzerinde önbelleğe alınmış içerik oluşturabilir. Diğer tüm istek yöntemleri ağ üzerinden proxy olarak kullanılır.
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>İstek düzeni

Istek şeması eşleştirme koşulu yalnızca, varlıklar seçili protokol aracılığıyla istendiğinde karşılanır. Kullanılabilir protokoller şunlardır:

- HTTP
- HTTPS

Anahtar bilgileri:

- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
  - Önbellek dolgusunu tamamla
  - Varsayılan dahili maksimum yaş
  - Maksimum Iç kullanım süresi
  - Önbellek yok sayma
  - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL yolu dizini

Bir isteği, istenen varlığın dosya adını dışlayan göreli yoluna göre tanımlar.

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL yolu dizin eşleşme koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin, belirtilen URL düzeniyle eşleşen dosya adı hariç göreli bir URL yolu içermesini gerektirir.
- **Eşleşmiyor**: İsteğin, belirtilen URL düzeniyle eşleşmeyen, dosya adı hariç göreli bir URL yolu içermesini gerektirir.

Anahtar bilgileri:

- URL karşılaştırmasının içerik erişim noktası öncesinde veya sonrasında başlayacağını belirtmek için **göreli** ' i kullanın seçeneğini kullanın. İçerik erişim noktası, Verizon CDN ana bilgisayar adı ile istenen varlığın göreli yolu arasında görünen yolun bölümüdür (örneğin,/800001/CustomerOrigin). Sunucu türüne göre bir konum tanımlar (örneğin, CDN veya müşteri kaynağı) ve müşteri hesap numaranız.

   **Göreli** seçenek için aşağıdaki değerler mevcuttur:
  - **Kök**: URL karşılaştırma noktasının CDN ana bilgisayar adından hemen sonra başlayacağını gösterir. 

  Örneğin: http:\//WPC.exe 01.&lt; etki&gt;alanı/**800001/myorigin/myFolder**/Index.htm

  - **Kaynak**: URL karşılaştırma noktasının içerik erişim noktası sonrasında başlayacağını belirtir (örneğin,/000001 veya/800001/myorigin). . Azureedge.net CNAME, varsayılan olarak Verizon CDN ana bilgisayar dizinindeki kaynak dizine göre oluşturulduğundan, Azure CDN kullanıcıların kaynak değerini kullanması gerekir. \* 

  Örneğin\/: https:/&lt;Endpoint&gt;. azureedge.net/**myFolder**/Index.htm 

  Bu URL, şu Verizon CDN ana bilgisayar adına işaret eder:\/http:/WPC. 0001.&lt; etki&gt;alanı/800001/myorigın/**myFolder**/Index.htm

- Sınır CNAME URL 'SI, URL karşılaştırmasından önce bir CDN URL 'sine yeniden yazılır.

    Örneğin, aşağıdaki URL 'lerin her ikisi de aynı varlığı işaret ettikten ve bu nedenle aynı URL yoluna sahip olabilir.
  - CDN URL 'si: http\/:/WPC.exe 01.&lt; etki&gt;alanı/800001/CustomerOrigin/Path/Asset.htm
    
  - Edge CNAME URL 'si: http\/:&gt;/&lt;Endpoint. azureedge.net/Path/Asset.htm
    
    Ek bilgiler:
  - Özel etki alanı: https\/:/My.domain.com/path/Asset.htm
    
    - URL yolu (köke göre):/800001/CustomerOrigin/path/
    
    - URL yolu (kaynağa göre):/Path/

- URL karşılaştırması için kullanılan URL 'nin bölümü, istenen varlığın dosya adından hemen önce sona erer. Sondaki eğik çizgi, bu yol türünün son karakteridir.

- URL yol deseninin içindeki tüm boşlukları "% 20" ile değiştirin.

- Her bir yıldız işareti bir veya daha fazla karakter dizisiyle eşleştiğinde, her URL yol deseninin bir veya daha fazla yıldız işareti (*) bulunabilir.

- Her birini tek bir boşlukla ayırarak, düzende birden fazla URL yolu belirtin.

    Örneğin: */Sales/*/Pazar/

- URL yolu belirtimi [Joker değerlerden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)faydalanabilir.

- Büyük/küçük harfe duyarlı bir karşılaştırmanın gerçekleştirilip gerçekleştirilmediğini denetlemek için **durumu yoksay** seçeneğini kullanın.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL yolu uzantısı

İstekleri istenen varlığın dosya uzantısına göre tanımlar.

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL yolu uzantısının eşleşme koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: Belirtilen Düzenle tam olarak eşleşen bir dosya uzantısı içermesi için isteğin URL 'sini gerektirir.

   Örneğin, "htm" belirtirseniz, "htm" varlıkları eşleştirilir ancak "html" varlıkları eşleştirilir.  

- **Eşleşmiyor**: URL isteğinin belirtilen Düzenle eşleşmeyen bir dosya uzantısı içermesini gerektirir.

Anahtar bilgileri:

- **Değer** kutusunda eşleştirilecek dosya uzantılarını belirtin. Baştaki nokta eklemeyin; Örneğin,. htm yerine htm kullanın.

- Büyük/küçük harfe duyarlı bir karşılaştırmanın gerçekleştirilip gerçekleştirilmediğini denetlemek için **durumu yoksay** seçeneğini kullanın.

- Her uzantıyı tek bir boşlukla ayırarak birden çok dosya uzantısı belirtin. 

    Örneğin: htm html

- Örneğin, "htm" belirtildiğinde "htm" varlıkları eşleşir, ancak "html" varlıkları eşleşmez.

#### <a name="sample-scenario"></a>Örnek senaryo

Aşağıdaki örnek yapılandırma, bir istek belirtilen uzantılardan biriyle eşleştiğinde bu eşleşme koşulunun karşılandığını varsayar.

Değer belirtimi: ASP aspx PHP HTML

Bu eşleştirme koşulu, aşağıdaki uzantılarla biten URL 'Leri bulduğunda karşılanır:

- .asp
- .aspx
- . php
- . html

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL yolu dosya adı

İstekleri istenen varlığın dosya adına göre tanımlar. Bu eşleşme koşulunun amaçları doğrultusunda, bir dosya adı istenen varlık, bir nokta ve dosya uzantısı (örneğin, index. html) adını içerir.

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL yolu dosya adı eşleştirme koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin URL yolunda belirtilen Düzenle eşleşen bir dosya adı içermesini gerektirir.
- **Eşleşmiyor**: İsteğin URL yolunda belirtilen Düzenle eşleşmeyen bir dosya adı içermesini gerektirir.

Anahtar bilgileri:

- Büyük/küçük harfe duyarlı bir karşılaştırmanın gerçekleştirilip gerçekleştirilmediğini denetlemek için **durumu yoksay** seçeneğini kullanın.

- Birden çok dosya uzantısı belirtmek için, her uzantıyı tek bir boşlukla ayırın.

    Örneğin: index. htm index. html

- Dosya adı değerindeki boşlukları "% 20" ile değiştirin.

- Dosya adı değeri [Joker değerlerden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)faydalanabilir. Örneğin her bir dosya adı deseninin bir veya daha fazla yıldız işareti (*), her yıldız işareti bir veya daha fazla karakterle eşleşen bir dizi olabilir.

- Joker karakterler belirtilmemişse, bu eşleştirme koşulunu yalnızca tam bir eşleşme karşılar.

    Örneğin, "Presentation. ppt" belirtildiğinde "Presentation. ppt" adlı bir varlıkla eşleşir, ancak "Presentation. pptx" adı yoktur.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL yolu sabit değeri

Dosya adı da dahil olmak üzere bir isteğin URL yolunu belirtilen değere karşılaştırır.

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL yolu sabit değeri eşleşme koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin belirtilen Düzenle eşleşen bir URL yolu içermesini gerektirir.
- **Eşleşmiyor**: İsteğin, belirtilen Düzenle eşleşmeyen bir URL yolu içermesini gerektirir.

Anahtar bilgileri:

- URL karşılaştırma noktasının içerik erişim noktası öncesinde veya sonrasında başlayıp başlamamadığını belirtmek için **göreli** ' ı kullanın. 

    **Göreli** seçenek için aşağıdaki değerler mevcuttur:
  - **Kök**: URL karşılaştırma noktasının CDN ana bilgisayar adından hemen sonra başlayacağını gösterir.

    Örneğin: http:\//WPC.exe 01.&lt; etki&gt;alanı 800001/myorigin/myFolder/index.htm/

  - **Kaynak**: URL karşılaştırma noktasının içerik erişim noktası sonrasında başlayacağını belirtir (örneğin,/000001 veya/800001/myorigin). . Azureedge.net CNAME, varsayılan olarak Verizon CDN ana bilgisayar dizinindeki kaynak dizine göre oluşturulduğundan, Azure CDN kullanıcıların kaynak değerini kullanması gerekir. \* 

    Örneğin\/: https:/&lt;Endpoint&gt;. azureedge.net/**myFolder/index.htm**

  Bu URL, şu Verizon CDN ana bilgisayar adına işaret eder:\/http:/WPC. 0001.&lt; etki&gt;alanı/800001/myorigin/**myFolder/index.htm**

- Bir sınır CNAME URL 'SI bir URL karşılaştırmasından önce CDN URL 'sine yeniden yazılır.

Örneğin, aşağıdaki URL 'lerin her ikisi de aynı varlığı işaret ettikten ve bu nedenle aynı URL yoluna sahiptir:

- CDN URL 'si: http\/:/WPC.exe 01.&lt; etki&gt;alanı/800001/CustomerOrigin/Path/Asset.htm
- Edge CNAME URL 'si: http\/:&gt;/&lt;Endpoint. azureedge.net/Path/Asset.htm

    Ek bilgiler:
    
    - URL yolu (köke göre):/800001/CustomerOrigin/path/asset.htm
   
    - URL yolu (kaynağa göre):/path/asset.htm

- URL 'deki sorgu dizeleri yok sayılır.
- Büyük/küçük harfe duyarlı bir karşılaştırmanın gerçekleştirilip gerçekleştirilmediğini denetlemek için **durumu yoksay** seçeneğini kullanın.
- Bu eşleştirme koşulu için belirtilen değer, istemci tarafından yapılan tam isteğin göreli yoluyla karşılaştırılır.

- Belirli bir dizine yapılan tüm istekleri eşleştirmek için [URL yol dizinini](#url-path-directory) veya [URL yolu](#url-path-wildcard) eşleştirme koşulunu kullanın.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL yolu Regex

Bir isteğin URL yolunu belirtilen [normal ifadeyle](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)karşılaştırır.

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL yolu Regex ile eşleşen koşulun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin, belirtilen normal ifadeyle eşleşen bir URL yolu içermesini gerektirir.
- **Eşleşmiyor**: İsteğin, belirtilen normal ifadeyle eşleşmeyen bir URL yolu içermesini gerektirir.

Anahtar bilgileri:

- Bir Edge CNAME URL 'SI, URL karşılaştırmasından önce bir CDN URL 'sine yeniden yazılır.

    Örneğin, her iki URL aynı varlığı işaret ettikten ve bu nedenle aynı URL yoluna sahip olabilir.

     - CDN URL 'si: http\/:/WPC.exe 01.&lt; etki&gt;alanı/800001/CustomerOrigin/Path/Asset.htm

     - Edge CNAME URL 'si: http\/:/My.domain.com/path/Asset.htm

    Ek bilgiler:
    
     - URL yolu:/800001/CustomerOrigin/path/asset.htm

- URL 'deki sorgu dizeleri yok sayılır.
    
- Büyük/küçük harfe duyarlı bir karşılaştırmanın gerçekleştirilip gerçekleştirilmediğini denetlemek için **durumu yoksay** seçeneğini kullanın.
    
- URL yolundaki boşluklar "% 20" ile değiştirilmelidir.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>URL yolu joker karakteri

Bir isteğin göreli URL yolunu belirtilen joker karakter düzeniyle karşılaştırır.

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL yolu joker karakter eşleşme koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin, belirtilen joker karakter düzeniyle eşleşen bir URL yolu içermesini gerektirir.
- **Eşleşmiyor**: İsteğin, belirtilen joker karakter düzeniyle eşleşmeyen bir URL yolu içermesini gerektirir.

Anahtar bilgileri:

- Seçeneğe **göre** : Bu seçenek, URL karşılaştırma noktasının içerik erişim noktası öncesinde veya sonrasında başlayıp başlamadığını belirler.

   Bu seçenek aşağıdaki değerlere sahip olabilir:
     - **Kök**: URL karşılaştırma noktasının CDN ana bilgisayar adından hemen sonra başlayacağını gösterir.

       Örneğin: http:\//WPC.exe 01.&lt; etki&gt;alanı 800001/myorigin/myFolder/index.htm/

     - **Kaynak**: URL karşılaştırma noktasının içerik erişim noktası sonrasında başlayacağını belirtir (örneğin,/000001 veya/800001/myorigin). . Azureedge.net CNAME, varsayılan olarak Verizon CDN ana bilgisayar dizinindeki kaynak dizine göre oluşturulduğundan, Azure CDN kullanıcıların kaynak değerini kullanması gerekir. \* 

       Örneğin\/: https:/&lt;Endpoint&gt;. azureedge.net/**myFolder/index.htm**

     Bu URL, şu Verizon CDN ana bilgisayar adına işaret eder:\/http:/WPC. 0001.&lt; etki&gt;alanı/800001/myorigin/**myFolder/index.htm**

- Bir Edge CNAME URL 'SI, URL karşılaştırmasından önce bir CDN URL 'sine yeniden yazılır.

    Örneğin, aşağıdaki URL 'lerin her ikisi de aynı varlığı işaret ettikten ve bu nedenle aynı URL yoluna sahiptir:
     - CDN URL 'si http://wpc.0001.&lt:;d omain&gt; /800001/CustomerOrigin/Path/Asset.htm
     - Edge CNAME URL 'si: http\/:&gt;/&lt;Endpoint. azureedge.net/Path/Asset.htm
    
    Ek bilgiler:
    
     - URL yolu (köke göre):/800001/CustomerOrigin/path/asset.htm
    
     - URL yolu (kaynağa göre):/path/asset.htm
    
- Her birini tek bir boşlukla ayırarak birden çok URL yolu belirtin.

   Örneğin:/Pazar/Asset. */Sales/*. htm

- URL 'deki sorgu dizeleri yok sayılır.
    
- Büyük/küçük harfe duyarlı bir karşılaştırmanın gerçekleştirilip gerçekleştirilmediğini denetlemek için **durumu yoksay** seçeneğini kullanın.
    
- URL yolundaki boşlukları "% 20" ile değiştirin.
    
- URL yolu için belirtilen değer [Joker değerlerden](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)faydalanabilir. Her bir yıldız işareti bir veya daha fazla karakter dizisiyle eşleşiyorsa, her URL yol deseninin bir veya daha fazla yıldız işareti (*) bulunabilir.

#### <a name="sample-scenarios"></a>Örnek senaryolar

Aşağıdaki tabloda yer alan örnek yapılandırmalarda, bir istek belirtilen URL düzeniyle eşleştiğinde bu eşleşme koşulunun karşılandığı varsayılır:

Değer                   | Göreli    | Sonuç 
------------------------|----------------|-------
*/testpst HTML */test.exe  | Kök veya kaynak | Bu model, herhangi bir klasörde "test. html" veya "test. php" adlı varlıklar için isteklerle eşleştirilir.
/80ABCD/origin/text/*   | Kök           | Bu model, istenen varlık aşağıdaki ölçütlere uyduğunda eşleştirilir: <br />-Bu, "Origin" adlı bir müşteri kaynağı üzerinde bulunmalıdır. <br />-Göreli yol "metin" adlı bir klasörle başlamalıdır. Diğer bir deyişle, istenen varlık "metin" klasöründe ya da özyinelemeli alt klasörlerinden birinde bulunabilir.
*/CSS/* */js/*          | Kök veya kaynak | Bu model, CSS veya JS klasörü içeren tüm CDN veya Edge CNAME URL 'Leri ile eşleştirilir.
*.jpg *.gif *.png       | Kök veya kaynak | Bu model,. jpg,. gif veya. png ile biten tüm CDN veya Edge CNAME URL 'Leri ile eşleştirilir. Bu kalıbı belirtmenin alternatif bir yolu, [URL yolu uzantısı eşleşme koşuluna](#url-path-extension)sahiptir.
/images/*/Media/*      | Kaynak         | Bu model, göreli yolu bir "görüntüler" veya "medya" klasörüyle başlayan CDN veya Edge CNAME URL 'Leri ile eşleştirilir. <br />-CDN URL 'si: http\/:/wpcpst 01.&lt; etki&gt;alanı/800001/myorigin/images/Sales/Event1.png<br />-Örnek Edge CNAME URL 'si: http\/:/cdn.mydomain.com/images/Sales/Event1.png

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL sorgusu sabit değeri

Bir isteğin sorgu dizesini belirtilen değerle karşılaştırır.

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL sorgusunun değişmez değer eşleşmesi koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin, belirtilen sorgu dizesiyle eşleşen bir URL sorgu dizesi içermesini gerektirir.
- **Eşleşmiyor**: İsteğin, belirtilen sorgu dizesiyle eşleşmeyen bir URL sorgu dizesi içermesini gerektirir.

Anahtar bilgileri:

- Bu eşleşme koşulunu karşılayan yalnızca tam sorgu dizesi eşleşiyor.
    
- Sorgu dizesi karşılaştırmalarının büyük/küçük harf duyarlılığını denetlemek için **durum yoksay** seçeneğini kullanın.
    
- Sorgu dizesi değer metnine önde gelen soru işareti (?) eklemeyin.
    
- Belirli karakterler URL kodlaması gerektirir. Aşağıdaki karakterleri kodlayan URL için yüzde simgesini kullanın:

   Karakter | URL kodlaması
   ----------|---------
   Boşluk     | %20
   &         | %25

- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
   - Önbellek dolgusunu tamamla
   - Varsayılan dahili maksimum yaş
   - Maksimum Iç kullanım süresi
   - Önbellek yok sayma
   - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>URL sorgu parametresi

Belirtilen sorgu dizesi parametresini içeren istekleri tanımlar. Bu parametre, belirtilen bir düzenle eşleşen bir değere ayarlanır. İstek URL 'sindeki sorgu dizesi parametreleri (örneğin, Parameter = değer) bu koşulun karşılanıp karşılanmadığını belirtir. Bu eşleştirme koşulu, bir sorgu dizesi parametresini adına göre tanımlar ve parametre değeri için bir veya daha fazla değeri kabul eder. 

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL sorgu parametresi eşleşme koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: Bu eşleşme koşulunda tanımlanan değerlerden en az biriyle eşleşen bir değere sahip belirtilen parametreyi içeren bir istek gerektirir.
- **Eşleşmiyor**: İsteğin aşağıdaki ölçütlerden birini karşılaması gerekir:
  - Belirtilen parametresi içermiyor.
  - Belirtilen parametreyi içeriyor, ancak değeri bu eşleşme koşulunda tanımlanan değerlerden hiçbiriyle eşleşmiyor.

Bu eşleştirme koşulu, parametre adı/değer birleşimlerini belirtmenin kolay bir yolunu sağlar. Bir sorgu dizesi parametresiyle eşleşen daha fazla esneklik için [URL sorgusu joker karakter](#url-query-wildcard) eşleştirme koşulunu kullanmayı düşünün.

Anahtar bilgileri:

- Bu eşleşme koşulunun örneği başına yalnızca tek bir URL sorgu parametresi adı belirtilebilir.
    
- Bir parametre adı belirtildiğinde joker karakter değerleri desteklenmediğinden, yalnızca tam parametre adı karşılaştırma için uygun olur.
- Parametre değerleri, [Joker değerler](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)içerebilir.
   - Her bir yıldız işaretiyle bir veya daha fazla karakter dizisiyle eşleşen her bir parametre değeri stili bir veya daha fazla yıldız işareti (*) içerebilir.
   - Belirli karakterler URL kodlaması gerektirir. Aşağıdaki karakterleri kodlayan URL için yüzde simgesini kullanın:

       Karakter | URL kodlaması
       ----------|---------
       Boşluk     | %20
       &         | %25

- Her birini tek bir boşlukla ayırarak birden çok sorgu dizesi parametre değeri belirtin. Bir istek belirtilen ad/değer birleşimlerinden birini içerdiğinde, bu eşleşme koşulu karşılanır.

   - Örnek 1:

     - Yapılandırma:

       Değerea ValueB

     - Bu yapılandırma aşağıdaki sorgu dizesi parametreleriyle eşleşir:

       Parametre1 = Değerea
    
       Parametre1 = ValueB

   - Örnek 2:

     - Yapılandırma: 

        Değer% 20A değer% 20B

     - Bu yapılandırma aşağıdaki sorgu dizesi parametreleriyle eşleşir:

       Parameter1=Value%20A

       Parameter1=Value%20B

- Bu eşleştirme koşulu yalnızca belirtilen sorgu dizesi ad/değer birleşimlerinden en az biri ile tam eşleşme olduğunda karşılanır.

   Örneğin, önceki örnekte yapılandırmayı kullanırsanız, "parametre1 = ValueAdd" parametre adı/değer birleşimi eşleşme olarak kabul edilmez. Ancak, aşağıdaki değerlerden birini belirtirseniz, bu ad/değer birleşimiyle eşleşir:

   - ValueA ValueB ValueAdd
   - ValueA * ValueB

- Sorgu dizesi karşılaştırmalarının büyük/küçük harf duyarlılığını denetlemek için **durum yoksay** seçeneğini kullanın.
    
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
   - Önbellek dolgusunu tamamla
   - Varsayılan dahili maksimum yaş
   - Maksimum Iç kullanım süresi
   - Önbellek yok sayma
   - İç maksimum-eski

#### <a name="sample-scenarios"></a>Örnek senaryolar

Aşağıdaki örnek, bu seçeneğin belirli durumlarda nasıl çalıştığını göstermektedir:

Ad  | Değer |  Sonuç
------|-------|--------
Kullanıcı  | Joe   | İstenen URL için sorgu dizesi "? User = ali" olduğunda bu model eşleştirilir.
Kullanıcı  | *     | Bu model, istenen URL için sorgu dizesi bir Kullanıcı parametresi içerdiğinde eşleştirilir.
Email | Joe\* | Bu model, istenen bir URL için sorgu dizesi "ali" ile başlayan bir e-posta parametresi içerdiğinde eşleştirilir.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL sorgu Regex

Belirtilen sorgu dizesi parametresini içeren istekleri tanımlar. Bu parametre, belirtilen [normal ifadeyle](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)eşleşen bir değere ayarlanır.

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL sorgusu Regex ile eşleşen koşulun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin, belirtilen normal ifadeyle eşleşen bir URL sorgu dizesi içermesini gerektirir.
- **Eşleşmiyor**: İsteğin, belirtilen normal ifadeyle eşleşmeyen bir URL sorgu dizesi içermesini gerektirir.

Anahtar bilgileri:

- Belirtilen normal ifadeyle yalnızca tam eşleşmeler bu eşleştirme koşulunu karşılar.
    
- Sorgu dizesi karşılaştırmalarının büyük/küçük harf duyarlılığını denetlemek için **durum yoksay** seçeneğini kullanın.
    
- Bu seçeneğin amaçları doğrultusunda sorgu dizesi, sorgu dizesi için soru işareti (?) sınırlayıcısından sonra ilk karakterle başlar.
    
- Belirli karakterler URL kodlaması gerektirir. Aşağıdaki karakterleri kodlayan URL için yüzde simgesini kullanın:

   Karakter | URL kodlaması | Value
   ----------|--------------|------
   Boşluk     | %20          | \%20
   &         | %25          | \%25

   Yüzde simgelerinin kaçışına sahip olması gerektiğini unutmayın.

- Normal ifadede ters eğik çizgi dahil etmek için, çift kaçış \^özel normal ifade karakterleri (örneğin, $. +).

   Örneğin:

   Value | Yorumlanan 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
   - Önbellek dolgusunu tamamla
   - Varsayılan dahili maksimum yaş
   - Maksimum Iç kullanım süresi
   - Önbellek yok sayma
   - İç maksimum-eski

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL sorgusu joker karakteri

Belirtilen değerleri isteğin sorgu dizesine göre karşılaştırır.

**Eşleşmeler**/**eşleşmiyor** seçeneği, URL sorgusunun joker karakter eşleşme koşulunun karşılandığı koşulları belirler.

- **Eşleşmeler**: İsteğin, belirtilen joker karakterle eşleşen bir URL sorgu dizesi içermesini gerektirir.
- **Eşleşmiyor**: İsteğin, belirtilen joker karakterle eşleşmeyen bir URL sorgu dizesi içermesini gerektirir.

Anahtar bilgileri:

- Bu seçeneğin amaçları doğrultusunda sorgu dizesi, sorgu dizesi için soru işareti (?) sınırlayıcısından sonra ilk karakterle başlar.
- Parametre değerleri [Joker değerler](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)içerebilir:
   - Her bir yıldız işaretiyle bir veya daha fazla karakter dizisiyle eşleşen her bir parametre değeri stili bir veya daha fazla yıldız işareti (*) içerebilir.
   - Belirli karakterler URL kodlaması gerektirir. Aşağıdaki karakterleri kodlayan URL için yüzde simgesini kullanın:

     Karakter | URL kodlaması
     ----------|---------
     Boşluk     | %20
     &         | %25

- Her birini tek bir boşlukla ayırarak birden çok değer belirtin.

   Örneğin: *Parametre1 = Değerea* *Valueb* *Parametre1 = ValueC & parametre2 = değer*

- Bu eşleşme koşulunu karşılayan belirtilen sorgu dizesi desenlerinden en az biri ile yalnızca tam eşleşmeler eşleşiyor.
    
- Sorgu dizesi karşılaştırmalarının büyük/küçük harf duyarlılığını denetlemek için **durum yoksay** seçeneğini kullanın.
    
- Önbellek ayarlarının izlenme şekli nedeniyle, bu eşleştirme koşulu aşağıdaki özelliklerle uyumlu değildir:
   - Önbellek dolgusunu tamamla
   - Varsayılan dahili maksimum yaş
   - Maksimum Iç kullanım süresi
   - Önbellek yok sayma
   - İç maksimum-eski

#### <a name="sample-scenarios"></a>Örnek senaryolar

Aşağıdaki örnek, bu seçeneğin belirli durumlarda nasıl çalıştığını göstermektedir:

 Ad                 | Açıklama
 ---------------------|------------
user=joe              | İstenen URL için sorgu dizesi "? User = ali" olduğunda bu model eşleştirilir.
\*Kullanıcı =\* \*OptOut =\* | CDN URL sorgusu Kullanıcı veya OptOut parametresi içerdiğinde bu model eşleştirilir.

[Başa dön](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Content Delivery Network genel bakış](cdn-overview.md)
- [Kural altyapısı başvurusu](cdn-verizon-premium-rules-engine-reference.md)
- [Kural altyapısı koşullu ifadeleri](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Kural altyapısı özellikleri](cdn-verizon-premium-rules-engine-reference-features.md)
- [Kurallar altyapısını kullanarak varsayılan HTTP davranışını geçersiz kılma](cdn-verizon-premium-rules-engine.md)

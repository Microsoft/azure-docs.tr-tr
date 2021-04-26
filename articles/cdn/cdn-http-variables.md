---
title: Azure CDN kuralları altyapısı için HTTP değişkenleri | Microsoft Docs
description: Bazı kural altyapısı özellikleri için HTTP isteği ve yanıt meta verilerini almanızı sağlayan HTTP değişkenleri hakkında bilgi edinin. Bir istek/yanıtı değiştirmek için meta verileri kullanın.
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
ms.date: 05/09/2018
ms.author: allensu
ms.openlocfilehash: a2d9fc98ba6f514afbd88e543a859a69e0fc6c6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192671"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN Rules altyapısı için HTTP değişkenleri
HTTP değişkenleri, HTTP isteği ve yanıt meta verilerini almak için kullanabileceğiniz yolları sağlar. Bu meta veriler, bir isteği veya yanıtı dinamik olarak değiştirmek için kullanılabilir. HTTP değişkenlerinin kullanımı aşağıdaki kural altyapısı özellikleriyle kısıtlıdır:

- [Cache-Key yeniden yazma](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm)
- [Istemci Isteği üst bilgisini Değiştir](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm)
- [Istemci yanıtı başlığını Değiştir](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm)
- [URL yeniden yönlendirme](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm)
- [URL yeniden yazma](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm)

## <a name="definitions"></a>Tanımlar
Aşağıdaki tabloda desteklenen HTTP değişkenleri açıklanmaktadır. COĞRAFI meta veriler (örneğin, posta kodu) belirli bir istek için kullanılamadığında boş bir değer döndürülür.


| Name | Değişken | Açıklama | Örnek değer |
| ---- | -------- | ----------- | ------------ |
| ASN (Istek sahibi) | % {geo_asnum} | İstek sahibinin numarasını belirtir. <br /><br />**Kullanım dışı:** % {virt_dst_asnum}. <br />Bu değişken,% {geo_asnum} için kullanım dışı bırakıldı. Kullanım dışı bırakılan bu değişkeni kullanan bir kural çalışmaya devam edebilse de, yeni değişkenini kullanmak için güncelleştirmeniz gerekir. | AS15133 |
| Şehir (Istek sahibi) | % {geo_city} | İsteyanın şehrini gösterir. | Los Angeles |
| Kıta (Istek sahibi) | % {geo_continent} | İstek sahibinin kısaltmasıyla kıolarak olduğunu gösterir. <br />Geçerli değerler: <br />AF: Afrika<br />AS: Asya<br />AB: Avrupa<br />NA: Kuzey Amerika<br />OC: Okyanusya<br />SA: Güney Amerika<br /><br />**Kullanım dışı:** % {virt_dst_continent}. <br />Bu değişken,% {geo_continent} için kullanım dışı bırakıldı. <br />Kullanım dışı bırakılan bu değişkeni kullanan bir kural çalışmaya devam edebilse de, yeni değişkenini kullanmak için güncelleştirmeniz gerekir.| Yok |
| Tanımlama bilgisi değeri | % {cookie_Cookie} | Tanımlama bilgisi terimi tarafından tanımlanan tanımlama bilgisi anahtarına karşılık gelen değeri döndürür. | Örnek kullanım: <br />% {cookie__utma}<br /><br />Örnek değer:<br />111662281.2.10.1222100123 |
| Ülke/bölge (Istek sahibi) | % {geo_country} | İstek sahibinin ülke/bölge kodu üzerinden kaynak ülkesini/bölgesini gösterir. <br />**Kullanım dışı:** % {virt_dst_country}. <br /><br />Bu değişken,% {geo_country} için kullanım dışı bırakıldı. Kullanım dışı bırakılan bu değişkeni kullanan bir kural çalışmaya devam edebilse de, yeni değişkenini kullanmak için güncelleştirmeniz gerekir. | ABD |
| Belirlenen pazar alanı (Istek sahibi) | % {geo_dma_code} |İsteyenin medya pazarını bölge koduna göre gösterir. <br /><br />Bu alan yalnızca Birleşik Devletler kaynaklı istekler için geçerlidir.| 745 |
| HTTP İsteği Yöntemi | % {request_method} | HTTP istek yöntemini gösterir. | GET |
| HTTP durum kodu | % {Status} | Yanıt için HTTP durum kodunu gösterir. | 200 |
| IP adresi (Istek sahibi) | % {virt_dst_addr} | İsteyenin IP adresini gösterir. | 192.168.1.1 |
| Enlem (Istek sahibi) | % {geo_latitude} | İsteyanın Enlem olduğunu gösterir. | 34,0995 |
| Boylam (Istek sahibi) | % {geo_longitude} | İsteyenin Boylam 'yi gösterir. | -118,4143 |
| Metropol Istatistiksel alanı (Istek sahibi) | % {geo_metro_code} | İsteyenin metropol alanını gösterir. <br /><br />Bu alan yalnızca Birleşik Devletler kaynaklı istekler için geçerlidir.<br />| 745 |
| Bağlantı noktası (Istek sahibi) | % {virt_dst_port} | İsteyanın kısa ömürlü bağlantı noktasını belirtir. | 55885 |
| Posta kodu (Istek sahibi) | % {geo_postal_code} | İsteyanın posta kodunu gösterir. | 90210 |
| Sorgu dizesi bulundu | % {is_args} | Bu değişkenin değeri, isteğin bir sorgu dizesi içerip içermediğini göre farklılık gösterir.<br /><br />-Sorgu dizesi bulundu:?<br />-Sorgu dizesi yok: NULL | ? |
| Sorgu dizesi parametresi bulundu | % {is_amp} | Bu değişkenin değeri, isteğin en az bir sorgu dizesi parametresi içerip içermediğini göre farklılık gösterir.<br /><br />-Parametre bulundu: &<br />-Parametre yok: NULL | & |
| Sorgu dizesi parametre değeri | % {arg_ &lt; parametresi &gt; } | Parametre terimi tarafından tanımlanan sorgu dizesi parametresine karşılık gelen değeri döndürür &lt; &gt; . | Örnek kullanım: <br />% {arg_language}<br /><br />Örnek sorgu dizesi parametresi: <br />? Language = en<br /><br />Örnek değer: en |
| Sorgu dizesi değeri | % {query_string} | İstek URL 'sinde tanımlanan tüm sorgu dizesi değerini gösterir. |KEY1 = val1&key2 = val2 & lt&Key3 = VAL3 |
| Başvuran etki alanı | % {referring_domain} | Başvuran istek üstbilgisinde tanımlanan etki alanını gösterir. | <www.google.com> |
| Bölge (Istek sahibi) | % {geo_region} | İstek sahibinin bölgesini (örneğin, eyalet veya bölge) alfasayısal kısaltmayla gösterir. | CA |
| İstek üst bilgisi değeri | % {http_RequestHeader} | RequestHeader terimi tarafından tanımlanan istek üst bilgisine karşılık gelen değeri döndürür. <br /><br />İstek üstbilgisinin adı bir tire içeriyorsa (örneğin, Kullanıcı Aracısı), bunu alt çizgiyle değiştirin (örneğin, User_Agent).| Örnek kullanım:% {http_Connection}<br /><br />Örnek değer: Keep-Alive | 
| İstek Konağı | % {Konak} | İstek URL 'sinde tanımlanan Konağı gösterir. | <www.mydomain.com> |
| İstek Protokolü | % {request_protocol} | İstek protokolünü gösterir. | HTTP/1.1 |
| İstek düzeni | % {Scheme} | İstek düzenini gösterir. |http |
| İstek URI 'SI (göreli) | % {request_uri} | Sorgu dizesi dahil olmak üzere, istek URI 'sinde tanımlanan göreli yolu gösterir. | /Pazar/foo.js? LoggedIn = doğru |
| İstek URI 'SI (sorgu dizesi olmadan göreli) | % {Uri} | İstenen içeriğin göreli yolunu gösterir. <br /><br/>Anahtar bilgileri:<br />-Bu göreli yol sorgu dizesini dışlar.<br />-Bu göreli yol, URL yeniden yazar yansıtır. Aşağıdaki koşullarda bir URL yeniden yazılır:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL yeniden yazma özelliği: Bu özellik, istek URI 'sinde tanımlanan göreli yolu yeniden yazar.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME URL 'SI: Bu istek türü, karşılık gelen CDN URL 'sine yeniden yazılır. |/800001/corigin/rewrittendir/foo.js |
| İstek URI'si | % {istek} | İsteği açıklar. <br />Sözdizimi: &lt; http yöntemi &gt; &lt; göreli yol &gt; &lt; http Protokolü&gt; | GET/Pazar/foo.js? LoggedIn = true HTTP/1.1 |
| Yanıt üst bilgisi değeri | % {resp_ &lt; ResponseHeader &gt; } | ResponseHeader terimi tarafından tanımlanan yanıt üstbilgisine karşılık gelen değeri döndürür &lt; &gt; . <br /><br />Yanıt üstbilgisinin adı bir tire içeriyorsa (örneğin, Kullanıcı Aracısı), bunu alt çizgiyle değiştirin (örneğin, User_Agent). | Örnek kullanım:% {resp_Content_Length}<br /><br />Örnek değer: 100 |

## <a name="usage"></a>Kullanım
Aşağıdaki tabloda, bir HTTP değişkeni belirtmek için uygun sözdizimi açıklanmaktadır.


| Syntax | Örnek | Description |
| ------ | -------- | ---------- |
| % { &lt; Httpvariable &gt; } | % {Konak} | Belirtilen httpvariable öğesine karşılık gelen değerin tamamını almak için bu sözdizimini kullanın &lt; &gt; . |
| % { &lt; Httpvariabledelimiter &gt; } | % {Ana bilgisayar,} | Belirtilen httpvariablesınırlayıcısına karşılık gelen değerin tamamı için büyük/küçük harf ayarlamak için bu sözdizimini kullanın  &lt; &gt; . |
| % { &lt; Httpvariablesınırlandıran terexpression &gt; } | % {Host/= ^ www \. ([^ \. ] +) \. ([^ \. :] +)/CDN. $2. $3:80} | &lt; &gt; Bir http değişkeninin değerini değiştirmek, silmek veya işlemek için httpvariablesınırlandıran terexpression için normal bir ifade kullanın. |

HTTP değişken adları yalnızca alfabetik karakterleri ve alt çizgileri destekler. Desteklenmeyen karakterleri alt çizgilere Dönüştür.

## <a name="delimiter-reference"></a>Sınırlayıcı başvurusu
Bir sınırlayıcı, aşağıdaki etkilerden herhangi birine ulaşmak için bir HTTP değişkeninden sonra belirtilebilir:

- Değişkenle ilişkili değeri dönüştürün.

     Örnek: tüm değeri küçük harfe Dönüştür.

- Değişkenle ilişkili değeri silin.

- Değişkenle ilişkili değeri değiştirin.

     Örnek: HTTP değişkeniyle ilişkili değeri değiştirmek için normal ifadeler kullanın.

Sınırlayıcılar aşağıdaki tabloda açıklanmıştır.

| Sınırlayıcı | Description |
| --------- | ----------- |
| := | Değişkene varsayılan bir değer atandığını belirtir: <br />-Eksik <br />-NULL olarak ayarlayın. |
| :+ | Kendisine bir değer atandığında değişkene varsayılan bir değer atanacağını belirtir. |
| : | Değişkene atanan değerin bir alt dizenin genişletilmeyeceğini belirtir. |
| # | Bu sınırlayıcıdan sonra belirtilen düzenin değişkenle ilişkili değerin başlangıcında bulunduğunda silineceğini belirtir. |
| % | Bu sınırlayıcıdan sonra belirtilen düzenin değişkenle ilişkili değerin sonunda bulunduğunda silineceğini belirtir. <br />Bu tanım yalnızca,% simgesi sınırlayıcı olarak kullanıldığında geçerlidir. |
| / | Bir HTTP değişkenini veya bir kalıbı ayırır. |
| // | Belirtilen düzenin tüm örneklerini bulun ve değiştirin. |
| /= | Belirtilen düzenin tüm oluşumlarını bulun, kopyalayın ve yeniden yazın. |
| , | HTTP değişkeniyle ilişkili değeri küçük harfe dönüştürür. |
| ^ | HTTP değişkeniyle ilişkili değeri büyük harfe Dönüştür. |
| ,, | HTTP değişkeniyle ilişkili değer içindeki belirtilen karakterin tüm örneklerini küçük harfe Dönüştür. |
| ^^ | HTTP değişkeniyle ilişkili değer içindeki belirtilen karakterin tüm örneklerini büyük harfe Dönüştür. |

## <a name="exceptions"></a>Özel durumlar
Aşağıdaki tabloda, belirtilen metnin bir HTTP değişkeni olarak değerlendirilmeyeceği koşullar açıklanmaktadır.

| Koşul | Açıklama | Örnek |
| --------- | ----------- | --------|
| Kaçış% simgesi | Yüzde simgesine bir ters eğik çizgi kullanılarak kaçışın. <br />Sağdaki örnek değer bir HTTP değişkeni olarak değil, değişmez değer olarak değerlendirilir.| \%konağının |
| Bilinmeyen değişkenler | Bilinmeyen değişkenler için her zaman boş bir dize döndürülür. | % {unknown_variable} |
| Geçersiz karakterler veya sözdizimi | Geçersiz karakterler veya sözdizimi içeren değişkenler değişmez değer olarak değerlendirilir. <br /><br />Örnek #1: belirtilen değer geçersiz bir karakter içeriyor (örneğin,-). <br /><br />Örnek #2: belirtilen değer çift küme ayracı kümesi içeriyor. <br /><br />Örnek #3: belirtilen değerde bir kapanış küme ayracı eksik.<br /> | Örnek #1:% {resp_user-Agent} <br /><br />Örnek #2:% {{Host}} <br /><br />Örnek #3:% {Ana bilgisayar |
| Eksik değişken adı | Bir değişken belirtilmediğinde NULL değeri her zaman döndürülür. | %{} |
| Sondaki karakterler | Bir değişkeni izleyen karakterler değişmez değer olarak değerlendirilir. <br />Sağdaki örnek değeri, sabit değer olarak değerlendirilecek bir sondaki küme ayracı içerir. | % {Konak}} |

## <a name="setting-default-header-values"></a>Varsayılan üstbilgi değerlerini ayarlama
Aşağıdaki koşullardan herhangi birini karşıladığında, bir üst bilgiye varsayılan değer atanabilir:
- Eksik/unset
- NULL olarak ayarlayın.

Aşağıdaki tabloda, varsayılan bir değerin nasıl tanımlanacağı açıklanmaktadır.

| Koşul | Syntax | Örnek | Description |
| --------- | ------ | --------| ----------- |
| Aşağıdaki koşullardan herhangi birini karşıladığında bir üst bilgiyi varsayılan değere ayarlayın: <br /><br />-Eksik üst bilgi <br /><br />-Header değeri NULL olarak ayarlandı.| % {Variable: = değer} | % {http_referrer: = belirtilmemiş} | Başvuran üst bilgisi yalnızca eksik ya da NULL olarak ayarlandığında *belirtilmemiş* olarak ayarlanır. Ayarlandıysa hiçbir eylem gerçekleşmeyecektir. |
| Bir üst bilgiyi, eksik olduğunda varsayılan değere ayarlayın. | % {Variable = değer} | % {http_referrer = belirtilmemiş} | Başvuran üst bilgisi yalnızca eksik olduğunda *belirtilmemiş* olarak ayarlanır. Ayarlandıysa hiçbir eylem gerçekleşmeyecektir. |
| Aşağıdaki koşullardan herhangi birini karşılamıyorsa üstbilgiyi varsayılan değere ayarlayın: <br /><br />-Eksik<br /><br /> -NULL olarak ayarlayın. | % {Variable: + Value} | % {http_referrer: + belirtilmemiş} | Başvuran üst bilgisi yalnızca bir değer atandığında *belirtilmemiş* olarak ayarlanır. Eksik ya da NULL olarak ayarlandıysa hiçbir eylem gerçekleşmeyecektir. |

## <a name="manipulating-variables"></a>Değişkenleri düzenleme
Değişkenler aşağıdaki yollarla yönetilebilir:

- Alt dizeleri genişletme
- Desenleri kaldırma

### <a name="substring-expansion"></a>Alt dize genişletmesi
Varsayılan olarak, bir değişken tam değerine genişletilir. Yalnızca değişkenin değerinin bir alt dizesini genişletmek için aşağıdaki sözdizimini kullanın.

`%<Variable>:<Offset>:<Length>}`

Anahtar bilgileri:

- Konum terimine atanan değer, alt dizenin başlangıç karakterini belirler:

     - Pozitif: alt dizenin başlangıç karakteri dizedeki ilk karakterden hesaplanır.
     - Sıfır: alt dizenin başlangıç karakteri dizedeki ilk karakterdir.
     - Negatif: alt dizenin başlangıç karakteri dizedeki son karakterden hesaplanır.

- Alt dizenin uzunluğu *uzunluk uzunluğuna* göre belirlenir:

     - Atlandı: Uzunluk terimi atlanırsa, dizenin başlangıç karakteri ile dizenin sonu arasındaki tüm karakterleri içermesini sağlar.
     - Pozitif: başlangıç karakterinden sağdaki alt dizenin uzunluğunu belirler.
     - Negatif: başlangıç karakterinden sol taraftaki alt dizenin uzunluğunu belirler.

#### <a name="example"></a>Örnek:

Aşağıdaki örnek, aşağıdaki örnek istek URL 'sini kullanır:

https: \/ /cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Aşağıdaki dize, değişkenleri işlemek için çeşitli yöntemleri göstermektedir:

https: \/ /www%{HTTP_HOST: 3}/Mobile/%{request_uri: 7:10}/% {request_uri:-5:-8}. htm

Örnek istek URL 'sine bağlı olarak, yukarıdaki değişken işleme aşağıdaki değeri üretir:

https: \/ /www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Model kaldırma
Belirli bir düzenle eşleşen metin, bir değişkenin değerinin başından veya sonundan kaldırılabilir.

| Syntax | Eylem |
| ------ | ------ |
| % {Değişken # model} | Bir değişkenin değerinin başlangıcında belirtilen model bulunduğunda metni kaldır. |
| % {Değişken% model} | Bir değişkenin değerinin sonunda belirtilen model bulunduğunda metni kaldır. |

#### <a name="example"></a>Örnek:

Bu örnek senaryoda *request_uri* değişkeni şu şekilde ayarlanır:

`/800001/myorigin/marketing/product.html?language=en-US`

Aşağıdaki tabloda bu sözdiziminin nasıl çalıştığı gösterilmektedir.

| Örnek sözdizimi | Sonuçlar | Description |
| ------------- | ------- | --- |
| % {request_uri #/800001}/customerorigin | /customerorigin/myorigin/Pazar/product.html? Language = en-US | Değişken, düzeniyle başladığı için değiştirildi. |
| % {request_uri% html} htm | /800001/myorigin/Pazar/product.html? dil = en-US | Değişken, Düzenle bitmediği için değişiklik yoktu.|

### <a name="find-and-replace"></a>Bulma ve değiştirme
Bul ve Değiştir sözdizimi aşağıdaki tabloda açıklanmıştır.

| Syntax | Eylem |
| ------ | ------ |
| % {Değişken/Bul/Değiştir} | Belirtilen düzenin ilk oluşumunu bul ve Değiştir. |
| % {Değişken//Bul/Değiştir} | Belirtilen düzenin tüm oluşumlarını bul ve Değiştir. |
| % {Değişken ^} |Tüm değeri büyük harfe dönüştürür. |
| % {Değişken ^ bul} | Belirtilen düzenin ilk oluşumunu büyük harfe Dönüştür. |
| % {Variable,} | Değerin tamamını küçük harfe dönüştürür. |
| % {Değişken, bul} | Belirtilen düzenin ilk oluşumunu küçük harfe Dönüştür. |

### <a name="find-and-rewrite"></a>Bul ve yeniden yaz
Bul ve Değiştir 'teki bir varyasyon için, yeniden yazarken belirtilen Düzenle eşleşen metni kullanın. Bul ve yeniden yaz sözdizimi aşağıdaki tabloda açıklanmıştır.

| Syntax | Eylem |
| ------ | ------ |
| % {Variable/= bul/yeniden yaz} | Belirtilen düzenin tüm oluşumlarını bulun, kopyalayın ve yeniden yazın. |
| % {Variable/^ bul/yeniden yaz} | Değişkenin başlangıcında gerçekleştiği sırada belirtilen kalıbı bulun, kopyalayın ve yeniden yazın. |
| % {Variable/$Find/rewrite} | Değişkenin sonunda oluştuğunda belirtilen kalıbı bulun, kopyalayın ve yeniden yazın. |
| % {Variable/Find} | Belirtilen düzenin tüm oluşumlarını bulun ve silin. |

Anahtar bilgileri:

- Belirtilen desenle eşleşen metni genişleterek bir dolar işareti ve ardından tam tamsayı belirtin (örneğin, $1).

- Birden çok desen belirtilebilir. Düzenin belirtilen sıra, kendisine atanacak olan tamsayıyı belirler. Aşağıdaki örnekte, ilk model "www" ile eşleşir, ikinci model ikinci düzey etki alanıyla eşleşir ve üçüncü model en üst düzey etki alanıyla eşleşir:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Yeniden yazan değer herhangi bir metin ve bu yer tutucu birleşimini içerebilir.

    Önceki örnekte, ana bilgisayar adı `cdn.$2.$3:80` (örneğin, CDN.mydomain.com:80) olarak yeniden yazılır.

- Bir model yer tutucusunun durumu (örneğin, $1) aşağıdaki bayraklar aracılığıyla değiştirilebilir:
     - U: büyük harf genişletilmiş değeri.

         Örnek söz dizimi:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: küçük harf genişletilmiş değeri.

         Örnek söz dizimi:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Bir işlecin, düzeninden önce belirtilmesi gerekir. Belirtilen operatör, model yakalama davranışını belirler:

     - `=`: Belirtilen düzenin tüm tekrarlarının yakalanıp yeniden yazılması gerektiğini gösterir.
     - `^`: Yalnızca belirtilen Düzenle başlayan metnin yakalanabileceğini gösterir.
     - `$`: Yalnızca belirtilen Düzenle biten metnin yakalanacağını belirtir.
 
- */Yeniden yazma* değerini atlarsanız, düzeniyle eşleşen metin silinir.



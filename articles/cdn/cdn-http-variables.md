---
title: Azure CDN kuralları altyapısı için HTTP değişkenleri | Microsoft Docs
description: HTTP değişkenleri, HTTP isteği ve yanıt meta verilerini almanıza olanak sağlar.
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
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593811"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN Rules altyapısı için HTTP değişkenleri
HTTP değişkenleri, HTTP isteği ve yanıt meta verilerini almak için kullanabileceğiniz yolları sağlar. Bu meta veriler, bir isteği veya yanıtı dinamik olarak değiştirmek için kullanılabilir. HTTP değişkenlerinin kullanımı aşağıdaki kural altyapısı özellikleriyle kısıtlıdır:

- [Cache-Key yeniden yazma](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Istemci Isteği üst bilgisini Değiştir](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Istemci yanıtı başlığını Değiştir](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL yeniden yönlendirme](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL yeniden yazma](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Tanımlar
Aşağıdaki tabloda desteklenen HTTP değişkenleri açıklanmaktadır. COĞRAFI meta veriler (örneğin, posta kodu) belirli bir istek için kullanılamadığında boş bir değer döndürülür.


| Ad | Değişken | Açıklama | Örnek değer |
| ---- | -------- | ----------- | ------------ |
| ASN (Istek sahibi) | %{geo_asnum} | İstek sahibinin numarasını belirtir. <br /><br />**Kullanım dışı:** % {virt_dst_asnum}. <br />Bu değişken% {geo_asnum} için kullanım dışı bırakıldı. Kullanım dışı bırakılan bu değişkeni kullanan bir kural çalışmaya devam edebilse de, yeni değişkenini kullanmak için güncelleştirmeniz gerekir. | AS15133 |
| Şehir (Istek sahibi) | %{geo_city} | İsteyanın şehrini gösterir. | Los Angeles |
| Kıta (Istek sahibi) | %{geo_continent} | İstek sahibinin kısaltmasıyla kıolarak olduğunu gösterir. <br />Geçerli değerler şunlardır: <br />AF Afrika<br />GEREKTIĞI Asya<br />YAPILAN Avrupa<br />YANA Kuzey Amerika<br />OC Okyanusya<br />SA Güney Amerika<br /><br />**Kullanım dışı:** % {virt_dst_continent}. <br />Bu değişken% {geo_continent} için kullanım dışı bırakıldı. <br />Kullanım dışı bırakılan bu değişkeni kullanan bir kural çalışmaya devam edebilse de, yeni değişkenini kullanmak için güncelleştirmeniz gerekir.| Yok |
| Tanımlama bilgisi değeri | %{cookie_Cookie} | Tanımlama bilgisi terimi tarafından tanımlanan tanımlama bilgisi anahtarına karşılık gelen değeri döndürür. | Örnek kullanım: <br />%{cookie__utma}<br /><br />Örnek değer:<br />111662281.2.10.1222100123 |
| Ülke (Istek sahibi) | %{geo_country} | İstek sahibinin ülke kodu aracılığıyla kaynak ülkesini gösterir. <br />**Kullanım dışı:** % {virt_dst_country}. <br /><br />Bu değişken% {geo_country} için kullanım dışı bırakıldı. Kullanım dışı bırakılan bu değişkeni kullanan bir kural çalışmaya devam edebilse de, yeni değişkenini kullanmak için güncelleştirmeniz gerekir. | ABD |
| Belirlenen pazar alanı (Istek sahibi) | %{geo_dma_code} |İsteyenin medya pazarını bölge koduna göre gösterir. <br /><br />Bu alan yalnızca Birleşik Devletler kaynaklı istekler için geçerlidir.| 745 |
| HTTP Istek yöntemi | %{request_method} | HTTP istek yöntemini gösterir. | GET |
| HTTP durum kodu | % {Status} | Yanıt için HTTP durum kodunu gösterir. | 200 |
| IP adresi (Istek sahibi) | %{virt_dst_addr} | İsteyenin IP adresini gösterir. | 192.168.1.1 |
| Enlem (Istek sahibi) | %{geo_latitude} | İsteyanın Enlem olduğunu gösterir. | 34,0995 |
| Boylam (Istek sahibi) | %{geo_longitude} | İsteyenin Boylam 'yi gösterir. | -118,4143 |
| Metropol Istatistiksel alanı (Istek sahibi) | %{geo_metro_code} | İsteyenin metropol alanını gösterir. <br /><br />Bu alan yalnızca Birleşik Devletler kaynaklı istekler için geçerlidir.<br />| 745 |
| Bağlantı noktası (Istek sahibi) | %{virt_dst_port} | İsteyanın kısa ömürlü bağlantı noktasını belirtir. | 55885 |
| Posta kodu (Istek sahibi) | %{geo_postal_code} | İsteyanın posta kodunu gösterir. | 90210 |
| Sorgu dizesi bulundu | %{is_args} | Bu değişkenin değeri, isteğin bir sorgu dizesi içerip içermediğini göre farklılık gösterir.<br /><br />-Sorgu dizesi bulundu:?<br />-Sorgu dizesi yok: NULL | ? |
| Sorgu dizesi parametresi bulundu | %{is_amp} | Bu değişkenin değeri, isteğin en az bir sorgu dizesi parametresi içerip içermediğini göre farklılık gösterir.<br /><br />-Parametre bulundu: &<br />-Parametre yok: NULL | & |
| Sorgu dizesi parametre değeri | % {arg_&lt;parametresi&gt;} | &lt;Parametre&gt; terimi tarafından tanımlanan sorgu dizesi parametresine karşılık gelen değeri döndürür. | Örnek kullanım: <br />%{arg_language}<br /><br />Örnek sorgu dizesi parametresi: <br />? Language = en<br /><br />Örnek değer: en |
| Sorgu dizesi değeri | %{query_string} | İstek URL 'sinde tanımlanan tüm sorgu dizesi değerini gösterir. |KEY1 = val1 & key2 = val2 & lt & Key3 = VAL3 |
| Başvuran etki alanı | %{referring_domain} | Başvuran istek üstbilgisinde tanımlanan etki alanını gösterir. | < www. Google. com > |
| Bölge (Istek sahibi) | %{geo_region} | İstek sahibinin bölgesini (örneğin, eyalet veya bölge) alfasayısal kısaltmayla gösterir. | CA |
| İstek üst bilgisi değeri | %{http_RequestHeader} | RequestHeader terimi tarafından tanımlanan istek üst bilgisine karşılık gelen değeri döndürür. <br /><br />İstek üstbilgisinin adı bir tire içeriyorsa (örneğin, Kullanıcı Aracısı), bunu alt çizgiyle değiştirin (örneğin, User_Agent).| Örnek kullanım:% {http_Connection}<br /><br />Örnek değer: Canlı tut | 
| İstek Konağı | % {Konak} | İstek URL 'sinde tanımlanan Konağı gösterir. | < www. etkialanım. com > |
| İstek Protokolü | %{request_protocol} | İstek protokolünü gösterir. | HTTP/1.1 |
| İstek düzeni | % {Scheme} | İstek düzenini gösterir. |http |
| İstek URI 'SI (göreli) | %{request_uri} | Sorgu dizesi dahil olmak üzere, istek URI 'sinde tanımlanan göreli yolu gösterir. | /Pazar/YN/js? LoggedIn = doğru |
| İstek URI 'SI (sorgu dizesi olmadan göreli) | % {Uri} | İstenen içeriğin göreli yolunu gösterir. <br /><br/>Anahtar bilgileri:<br />-Bu göreli yol sorgu dizesini dışlar.<br />-Bu göreli yol, URL yeniden yazar yansıtır. Aşağıdaki koşullarda bir URL yeniden yazılır:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL yeniden yazma özelliği: Bu özellik, istek URI 'sinde tanımlanan göreli yolu yeniden yazar.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME URL 'SI: Bu tür bir istek ilgili CDN URL 'sine yeniden yazılır. |/800001/corigin/rewrittendir/YN. js |
| İstek URI'si | % {istek} | İsteği açıklar. <br />Sözdizimi &lt;Http yöntemi&gt; &lt;göreli yol&gt; http&lt;Protokolü&gt; | GET/Pazar/YN/js? LoggedIn = true HTTP/1.1 |
| Yanıt üst bilgisi değeri | % {resp_&lt;ResponseHeader&gt;} | &lt;ResponseHeader&gt; terimi tarafından tanımlanan yanıt üstbilgisine karşılık gelen değeri döndürür. <br /><br />Yanıt üstbilgisinin adı bir tire içeriyorsa (örneğin, Kullanıcı Aracısı), bunu alt çizgiyle değiştirin (örneğin, User_Agent). | Örnek kullanım:% {resp_Content_Length}<br /><br />Örnek değer: 100 |

## <a name="usage"></a>Kullanım
Aşağıdaki tabloda, bir HTTP değişkeni belirtmek için uygun sözdizimi açıklanmaktadır.


| Sözdizimi | Örnek | Açıklama |
| ------ | -------- | ---------- |
| % {&lt;Httpvariable&gt;} | % {Konak} | Belirtilen &lt;httpvariable&gt;öğesine karşılık gelen değerin tamamını almak için bu sözdizimini kullanın. |
| % {&lt;Httpvariabledelimiter&gt;} | % {Ana bilgisayar,} | Belirtilen &lt;httpvariablesınırlayıcısına&gt;karşılık gelen değerin tamamı için büyük/küçük harf ayarlamak için bu sözdizimini kullanın. |
| % {&lt;Httpvariablesınırlandıran terexpression&gt;} | % {Host/= ^ www\.([^\.] +)\.([^\.:] +)/CDN. $2. $3:80} | Bir http değişkeninin değerini değiştirmek &lt;, silmek veya işlemek için&gt; httpvariablesınırlandıran terexpression için normal bir ifade kullanın. |

HTTP değişken adları yalnızca alfabetik karakterleri ve alt çizgileri destekler. Desteklenmeyen karakterleri alt çizgilere Dönüştür.

## <a name="delimiter-reference"></a>Sınırlayıcı başvurusu
Bir sınırlayıcı, aşağıdaki etkilerden herhangi birine ulaşmak için bir HTTP değişkeninden sonra belirtilebilir:

- Değişkenle ilişkili değeri dönüştürün.

     Örnek: Değerin tamamını küçük harfe dönüştürür.

- Değişkenle ilişkili değeri silin.

- Değişkenle ilişkili değeri değiştirin.

     Örnek: HTTP değişkeniyle ilişkili değeri değiştirmek için normal ifadeler kullanın.

Sınırlayıcılar aşağıdaki tabloda açıklanmıştır.

| Sınırlayıcı | Açıklama |
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
| Bilinmeyen değişkenler | Bilinmeyen değişkenler için her zaman boş bir dize döndürülür. | %{unknown_variable} |
| Geçersiz karakterler veya sözdizimi | Geçersiz karakterler veya sözdizimi içeren değişkenler değişmez değer olarak değerlendirilir. <br /><br />Örnek #1: Belirtilen değer geçersiz bir karakter içeriyor (örneğin,-). <br /><br />Örnek #2: Belirtilen değer bir çift küme ayraçları kümesi içeriyor. <br /><br />Örnek #3: Belirtilen değerde bir kapanış küme ayracı eksik.<br /> | Örnek #1:% {resp_user-Agent} <br /><br />Örnek #2:% {{Host}} <br /><br />Örnek #3:% {Ana bilgisayar |
| Eksik değişken adı | Bir değişken belirtilmediğinde NULL değeri her zaman döndürülür. | %{} |
| Sondaki karakterler | Bir değişkeni izleyen karakterler değişmez değer olarak değerlendirilir. <br />Sağdaki örnek değeri, sabit değer olarak değerlendirilecek bir sondaki küme ayracı içerir. | % {Konak}} |

## <a name="setting-default-header-values"></a>Varsayılan üstbilgi değerlerini ayarlama
Aşağıdaki koşullardan herhangi birini karşıladığında, bir üst bilgiye varsayılan değer atanabilir:
- Eksik/unset
- NULL olarak ayarlayın.

Aşağıdaki tabloda, varsayılan bir değerin nasıl tanımlanacağı açıklanmaktadır.

| Koşul | Sözdizimi | Örnek | Açıklama |
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

     - Pozitif Alt dizenin başlangıç karakteri dizedeki ilk karakterden hesaplanır.
     - Sıfırlama Alt dizenin başlangıç karakteri dizedeki ilk karakterdir.
     - Negatif Alt dizenin başlangıç karakteri dizedeki son karakterden hesaplanır.

- Alt dizenin uzunluğu *uzunluk uzunluğuna* göre belirlenir:

     - Atlarsanız Length teriminin atlanması, alt dizenin başlangıç karakteri ve dizenin sonu arasındaki tüm karakterleri içermesini sağlar.
     - Pozitif Başlangıç karakterinden sağdaki alt dizenin uzunluğunu belirler.
     - Negatif Başlangıç karakterinden sol taraftaki alt dizenin uzunluğunu belirler.

#### <a name="example"></a>Örnek:

Aşağıdaki örnek, aşağıdaki örnek istek URL 'sini kullanır:

https:\//CDN.mydomain.com/Folder/Marketing/myConsultant/Proposal.html

Aşağıdaki dize, değişkenleri işlemek için çeşitli yöntemleri göstermektedir:

https:\//www%{HTTP_HOST: 3}/Mobile/%{request_uri: 7:10}/%{request_uri:-5:-8}. htm

Örnek istek URL 'sine bağlı olarak, yukarıdaki değişken işleme aşağıdaki değeri üretir:

https:\//www.mydomain.com/Mobile/Marketing/Proposal.htm


### <a name="pattern-removal"></a>Model kaldırma
Belirli bir düzenle eşleşen metin, bir değişkenin değerinin başından veya sonundan kaldırılabilir.

| Sözdizimi | Action |
| ------ | ------ |
| % {Değişken # model} | Bir değişkenin değerinin başlangıcında belirtilen model bulunduğunda metni kaldır. |
| % {Değişken% model} | Bir değişkenin değerinin sonunda belirtilen model bulunduğunda metni kaldır. |

#### <a name="example"></a>Örnek:

Bu örnek senaryoda, *request_uri* değişkeni şu şekilde ayarlanır:

`/800001/myorigin/marketing/product.html?language=en-US`

Aşağıdaki tabloda bu sözdiziminin nasıl çalıştığı gösterilmektedir.

| Örnek sözdizimi | Sonuçlar | |
| ------------- | ------- | --- |
| % {request_uri #/800001}/customerorigin | /customerorigin/myorigin/Pazar/productem HTML? Language = en-US | Değişken, düzeniyle başladığı için değiştirildi. |
| %{request_uri%html}htm | /800001/myorigin/Pazar/Product.exe? dil = en-US | Değişken, Düzenle bitmediği için değişiklik yoktu.|

### <a name="find-and-replace"></a>Bul ve Değiştir
Bul ve Değiştir sözdizimi aşağıdaki tabloda açıklanmıştır.

| Sözdizimi | Action |
| ------ | ------ |
| % {Değişken/Bul/Değiştir} | Belirtilen düzenin ilk oluşumunu bul ve Değiştir. |
| % {Değişken//Bul/Değiştir} | Belirtilen düzenin tüm oluşumlarını bul ve Değiştir. |
| % {Değişken ^} |Tüm değeri büyük harfe dönüştürür. |
| % {Değişken ^ bul} | Belirtilen düzenin ilk oluşumunu büyük harfe Dönüştür. |
| % {Variable,} | Değerin tamamını küçük harfe dönüştürür. |
| % {Değişken, bul} | Belirtilen düzenin ilk oluşumunu küçük harfe Dönüştür. |

### <a name="find-and-rewrite"></a>Bul ve yeniden yaz
Bul ve Değiştir 'teki bir varyasyon için, yeniden yazarken belirtilen Düzenle eşleşen metni kullanın. Bul ve yeniden yaz sözdizimi aşağıdaki tabloda açıklanmıştır.

| Sözdizimi | Action |
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

    Önceki örnekte, ana bilgisayar adı (örneğin, CDN.mydomain.com:80 `cdn.$2.$3:80` ) olarak yeniden yazılır.

- Bir model yer tutucusunun durumu (örneğin, $1) aşağıdaki bayraklar aracılığıyla değiştirilebilir:
     - U: Büyük harfli genişletilmiş değer.

         Örnek sözdizimi:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - GİRİŞİNDEKİ Genişletilmiş değer küçük harfle yazılmalıdır.

         Örnek sözdizimi:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Bir işlecin, düzeninden önce belirtilmesi gerekir. Belirtilen operatör, model yakalama davranışını belirler:

     - `=`: Belirtilen düzendeki tüm oluşumların yakalanıp yeniden yazılması gerektiğini belirtir.
     - `^`: Yalnızca belirtilen Düzenle başlayan metnin yakalanabileceğini gösterir.
     - `$`: Yalnızca belirtilen Düzenle biten metnin yakalanacağını belirtir.
 
- */Yeniden yazma* değerini atlarsanız, düzeniyle eşleşen metin silinir.



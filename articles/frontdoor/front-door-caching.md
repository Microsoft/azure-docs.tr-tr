---
title: Azure ön kapı-önbelleğe alma | Microsoft Docs
description: Bu makale, önbelleğe alma özelliği etkinleştirilmiş yönlendirme kurallarıyla ön kapıya yönelik davranışı anlamanıza yardımcı olur.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: d001a7a24d44c46a19bde08051e21d3ae3c5acb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538060"
---
# <a name="caching-with-azure-front-door"></a>Azure ön kapılı önbelleğe alma
Aşağıdaki belge ön kapı için önbelleğe almayı etkinleştirilen yönlendirme kurallarıyla ilgili davranışları belirtir. Ön kapı, dinamik site hızlandırma ve yük dengeleme özellikli bir modern Content Delivery Network (CDN), aynı zamanda diğer CDN gibi önbelleğe alma davranışlarını da destekler.

## <a name="delivery-of-large-files"></a>Büyük dosyaların teslimi
Azure ön kapısı, dosya boyutu üst sınırı olmadan büyük dosyalar sunar. Ön kapı, nesne parçalama adlı bir teknik kullanır. Büyük dosya isteği gönderildiğinde Front Door dosyayı arka uçtan küçük parçalar halinde alır. Tam veya bayt aralığı dosya isteği aldıktan sonra, ön kapılı ortam dosyayı 8 MB öbeklerine arka uca ister.

Öbek, ön kapılı ortama ulaştıktan sonra önbelleğe alınır ve anında kullanıcıya sunulur. Ön kapı daha sonra bir sonraki öbeği paralel olarak önceden getirir. Bu ön alım, içeriğin kullanıcının önüne bir öbek kalmasını sağlar ve gecikme süresini azaltır. Bu işlem, tüm dosya indirilene (isteniyorsa) veya istemci bağlantıyı kapattığında devam eder.

Bayt aralığı isteği hakkında daha fazla bilgi için, [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)' i okuyun.
Ön kapı, tüm dosyaları ön kapı önbelleğinde önbelleğe alınması gerekmeyen tüm öbekleri önbellekte önbelleğe alır. Dosya veya bayt aralıkları için istekleri önbellekten sunulur. Öbeklerin hepsi önbelleğe alınmamışsa, ön alma arka uca öbekleri istemek için kullanılır. Bu iyileştirme, arka ucun bayt aralığı isteklerini destekleme özelliğine dayanır. Arka uç, bayt aralığı isteklerini desteklemiyorsa, bu iyileştirme etkili olmaz.

## <a name="file-compression"></a>Dosya sıkıştırma
Ön kapı, kenardaki içeriği dinamik olarak sıkıştırarak istemcileriniz için daha küçük ve daha hızlı yanıt süresi elde edebilir. Bir dosyanın sıkıştırmaya uygun olabilmesi için, önbelleğe almanın etkinleştirilmesi ve dosyanın sıkıştırmaya uygun olması için bir MIME türünde olması gerekir. Şu anda, ön kapı bu listenin değiştirilmesine izin vermez. Geçerli liste:
- "uygulama/duyot"
- "uygulama/yazı tipi"
- "Application/Font-sfnt"
- "uygulama/JavaScript"
- "application/json"
- "Application/OpenType"
- "Application/OTF"
- "uygulama/PKCS7-MIME"
- "uygulama/TrueType"
- "uygulama/TTF",
- "application/vnd. MS-fontobject"
- "application/xhtml + xml"
- "application/xml"
- "application/xml + RSS"
- "application/x-Font-OpenType"
- "application/x-Font-TrueType"
- "application/x-Font-TTF"
- "application/x-httpd-CGI"
- "application/x-mpegurl"
- "application/x-OpenType"
- "application/x-OTF"
- "application/x-Perl"
- "application/x-TTF"
- "application/x-JavaScript"
- "yazı tipi/EOT"
- "yazı tipi/TTF"
- "yazı tipi/OTF"
- "yazı tipi/OpenType"
- "image/SVG + XML"
- "metin/CSS"
- "metin/CSV"
- "metin/html"
- "metin/JavaScript"
- "metin/js", "metin/düz"
- "metin/zengin metin"
- "metin/sekmeyle ayrılmış değerler"
- "metin/xml"
- "metin/x-betiği"
- "metin/x-bileşen"
- "metin/x-Java-kaynak"

Ayrıca, dosya aynı zamanda 1 KB ile 8 MB (dahil) arasında olmalıdır.

Bu profiller aşağıdaki sıkıştırma kodlamalarını destekler:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Bir istek gzip ve Brotli sıkıştırmasını destekliyorsa, Brotli sıkıştırması önceliklidir.</br>
Bir varlık isteği sıkıştırmayı belirttiğinde ve istek bir önbellek isabetsizliği ile sonuçlanırsa, ön kapı doğrudan POP sunucusunda varlığın sıkıştırmasını yapar. Daha sonra, sıkıştırılan dosya önbellekten sunulur. Elde edilen öğe bir Transfer-Encoding: öbekli ile döndürülür.

## <a name="query-string-behavior"></a>Sorgu dizesi davranışı
Ön kapıda, dosyaların sorgu dizesi içeren bir Web isteği için nasıl önbelleğe alınacağını denetleyebilirsiniz. Sorgu dizesi olan bir Web isteğinde, sorgu dizesi, isteğin bir soru işareti (?) sonrasında gerçekleşen bölümüdür. Sorgu dizesi bir veya daha fazla anahtar-değer çifti içerebilir; burada alan adı ve değeri bir eşittir işareti (=) ile ayrılmıştır. Her anahtar-değer çifti bir ve işareti (&) ile ayrılır. Örneğin, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Bir isteğin sorgu dizesinde birden fazla anahtar-değer çifti varsa, bunların sırası önemli değildir.
- **Sorgu dizelerini yoksay**: Bu modda, ön kapı Sorgu dizelerini istek sahibine ilk istekteki arka uca geçirir ve varlığı önbelleğe alır. Ön kapı ortamından sunulan varlığın tüm istekleri, önbelleğe alınmış varlık sona erene kadar Sorgu dizelerini yoksayar.

- **Her benzersiz URL 'Yi önbelleğe al**: Bu modda, sorgu dizesi dahil olmak üzere benzersiz bir URL 'si olan her istek kendi önbelleğine sahip benzersiz bir varlık olarak değerlendirilir. Örneğin, için bir isteğin arka ucunun yanıtı, `www.example.ashx?q=test1` ön kapı ortamında önbelleğe alınır ve aynı sorgu dizesiyle önbellekler için döndürülür. İçin bir istek `www.example.ashx?q=test2` , kendi yaşam süresi ayarıyla ayrı bir varlık olarak önbelleğe alınır.

## <a name="cache-purge"></a>Önbellek Temizleme

Varlığın yaşam süresi (TTL) sona erene kadar ön kapı önbellekleri varlıkları. İstemci süresi dolma süresi TTL olan bir varlık istediğinde, ön kapı ortamı, isteği karşılamak için varlığın yeni bir güncelleştirilmiş kopyasını alır ve ardından yenilenen önbelleği depolar.

Kullanıcılarınızın her zaman varlıklarınızın en son kopyasını elde ettiğinizden emin olmak için en iyi yöntem, varlıklarınızın her bir güncelleştirme için sürüm oluşturup yeni URL 'Ler olarak yayınlanmasına yöneliktir. Ön kapı, sonraki istemci istekleri için yeni varlıkları hemen alacak. Bazen önbelleğe alınmış içeriği tüm kenar düğümlerinden temizlemek ve bunları yeni güncelleştirilmiş varlıkları almaya zorlamak isteyebilirsiniz. Nedeni, Web uygulamanızdaki güncelleştirmelerden veya hatalı bilgiler içeren varlıkları hızlıca güncelleştirebileceksiniz.

Kenar düğümlerinden temizlemek istediğiniz varlıkları seçin. Tüm varlıkları temizlemek için **Tümünü Temizle**' yi seçin. Aksi takdirde, **yol** alanına temizlemek istediğiniz her varlığın yolunu girin.

Bu biçimler, temizlenecek yolların listelerinde desteklenir:

- **Tek yol temizleme**: bir varlığın tam yolunu (protokol ve etki alanı olmadan), dosya uzantısıyla (örneğin,/resim/strasbourg.png;) belirterek tek tek varlıkları temizleyin.
- **Joker karakter Temizleme**: yıldız işareti ( \* ) joker karakter olarak kullanılabilir. Bir uç nokta altındaki tüm klasörleri, alt klasörleri ve dosyaları yolda/ \* içinde siler ya da klasörü belirterek/ \* , örneğin,/resim/gibi belirli bir klasör altındaki tüm alt klasörleri ve dosyaları temizleyin \* .
- **Kök etki alanı temizleme**: yoldaki bitiş noktasının kökünü "/" ile temizleyin.

> [!NOTE]
> **Joker karakter etki alanlarını Temizleme**: Bu bölümde anlatıldığı gibi, temizleme için önbelleğe alınmış yollar belirtme, ön kapıla ilişkili herhangi bir joker karakter etki alanı için uygulanmaz. Şu anda joker etki alanlarını doğrudan temizlemeyi desteklemiyoruz. Belirli alt etki alanlarından yolları, bu hedefleyen alt etki alanını ve temizleme yolunu belirterek temizleyebilirsiniz. Örneğin, ön kapım varsa `*.contoso.com` , yazarak alt etki alanım varlıklarını temizlebilirim `foo.contoso.com` `foo.contoso.com/path/*` . Şu anda, temizleme içerik yolunda ana bilgisayar adlarının belirtilmesi, varsa joker karakter etki alanlarının alt etki alanları olarak kabul edilir.
>

Ön kapıda önbellek temizler, büyük/küçük harfe duyarsızdır. Bunlara ek olarak, sorgu dizesi belirsiz olur, yani bir URL 'nin temizlenmesi, tüm sorgu dizesi çeşitlemelerini temizler. 

## <a name="cache-expiration"></a>Önbellek süre sonu
Bir öğenin önbellekte ne kadar süreyle depolanacağını anlamak için aşağıdaki üstbilgiler sırası kullanılır:</br>
1. Cache-Control: s-maxage =\<seconds>
2. Cache-Control: Max-Age =\<seconds>
3. Bitiminden \<http-date>

Yanıtın Cache-Control: Private, Cache-Control: No-Cache ve Cache-Control gibi önbelleğe alınmayabileceği belirten yanıt üstbilgileri Cache-Control: No-Store kabul edilir.  Cache-Control yoksa, varsayılan davranış ön kapısının kaynağı, X 'in 1 ila 3 gün arasında rastgele bir şekilde çekileceği X miktarı için önbelleğe alır.

## <a name="request-headers"></a>İstek üst bilgileri

Önbelleğe alma kullanılırken aşağıdaki istek üstbilgileri bir arka uca iletilmez.
- İçerik Uzunluğu
- Transfer-Encoding

## <a name="cache-duration"></a>Önbellek süresi

Önbellek süresi hem ön kapı tasarımcısında hem de Rules altyapısında yapılandırılabilir. Ön kapı tasarımcısında ayarlanan önbellek süresi en düşük önbellek süresi olur. Kaynaktan önbellek denetim üst bilgisinde geçersiz kılma değerinden daha fazla TTL varsa, bu geçersiz kılma çalışmayacaktır. 

Kural altyapısı aracılığıyla ayarlanan önbellek süresi, gerçek bir önbellek geçersiz kıldır, yani, kaynak yanıt üst bilgisinin ne olduğuna bakılmaksızın geçersiz kılma değerini kullanacaktır.

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.

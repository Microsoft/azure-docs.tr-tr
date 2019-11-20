---
title: Azure ön kapı hizmeti-önbelleğe alma | Microsoft Docs
description: Bu makale, Azure ön kapı hizmeti 'nin arka uçlarınızın sistem durumunu nasıl izlediğini anlamanıza yardımcı olur
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 70ee0af0b39e80aa90d143303b3c522fbb3cc780
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839212"
---
# <a name="caching-with-azure-front-door-service"></a>Azure ön kapı hizmeti ile önbelleğe alma
Aşağıdaki belge ön kapı için önbelleğe almayı etkinleştirilen yönlendirme kurallarıyla ilgili davranışı belirtir.

## <a name="delivery-of-large-files"></a>Büyük dosyaların teslimi
Azure ön kapı hizmeti, dosya boyutu üst sınırı olmadan büyük dosyalar sunar. Ön kapı, nesne parçalama adlı bir teknik kullanır. Büyük dosya isteği gönderildiğinde Front Door dosyayı arka uçtan küçük parçalar halinde alır. Front Door ortamı tam veya bayt aralığı belirtilen bir dosya isteği aldıktan sonra dosyayı 8 MB'lık parçalar halinde arka uçtan ister.

</br>Öbek, ön kapılı ortama ulaştıktan sonra önbelleğe alınır ve anında kullanıcıya sunulur. Ön kapı daha sonra bir sonraki öbeği paralel olarak önceden getirir. Bu ön alım, içeriğin kullanıcının önüne bir öbek kalmasını sağlar ve gecikme süresini azaltır. Bu işlem, dosyanın tamamı indirilene kadar devam eder (isteniyorsa), tüm bayt aralıkları kullanılabilir (isteniyorsa) veya istemci bağlantıyı sonlandırır.

</br>Bayt aralığı isteği hakkında daha fazla bilgi için, [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)' i okuyun.
Ön kapı, alındıkları tüm öbekleri önbelleğe alır ve tüm dosyanın ön kapı önbelleğinde önbelleğe alınması gerekmez. Dosya veya bayt aralıklarının sonraki istekleri önbellekten sunulur. Öbeklerin hepsi önbelleğe alınmamışsa, ön uç, arka ucun öbeklerini istemek için kullanılır. Bu en iyi duruma getirme, arka ucun bayt aralığı isteklerini destekleme özelliğine dayanır; arka uç, bayt aralığı isteklerini desteklemiyorsa, bu iyileştirme etkili olmaz.

## <a name="file-compression"></a>Dosya sıkıştırma
Ön kapı, kenardaki içeriği dinamik olarak sıkıştırarak istemcileriniz için daha küçük ve daha hızlı bir yanıt elde edebilir. Tüm dosyalar sıkıştırmaya uygun. Ancak, bir dosyanın, sıkıştırma listesine uygun bir MIME türünde olması gerekir. Şu anda, ön kapı bu listenin değiştirilmesine izin vermiyor. Geçerli liste:</br>
- "uygulama/duyot"
- "uygulama/yazı tipi"
- "Application/Font-sfnt"
- "uygulama/JavaScript"
- "Application/JSON"
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
Bir varlık isteği sıkıştırmayı belirttiğinde ve istek bir önbellek isabetsizliği ile sonuçlanırsa, ön kapı varlık sıkıştırmasını doğrudan POP sunucusunda gerçekleştirir. Daha sonra, sıkıştırılan dosya önbellekten sunulur. Elde edilen öğe bir Transfer-Encoding: öbekli ile döndürülür.

## <a name="query-string-behavior"></a>Sorgu dizesi davranışı
Ön kapıda, dosyaların sorgu dizesi içeren bir Web isteği için nasıl önbelleğe alınacağını denetleyebilirsiniz. Sorgu dizesi olan bir Web isteğinde, sorgu dizesi, isteğin bir soru işareti (?) sonrasında gerçekleşen bölümüdür. Sorgu dizesi bir veya daha fazla anahtar-değer çifti içerebilir; burada alan adı ve değeri bir eşittir işareti (=) ile ayrılmıştır. Her anahtar-değer çifti bir ve işareti (&) ile ayrılır. Örneğin, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Bir isteğin sorgu dizesinde birden fazla anahtar-değer çifti varsa, bunların sırası önemli değildir.
- **Sorgu dizelerini yoksay**: varsayılan mod. Bu modda, ön kapı Sorgu dizelerini istek sahibine ilk istekteki arka uca geçirir ve varlığı önbelleğe alır. Ön kapı ortamından sunulan varlık için sonraki tüm istekler, önbelleğe alınmış varlık sona erene kadar Sorgu dizelerini yoksayar.

- **Her benzersiz URL 'Yi önbelleğe al**: Bu modda, sorgu dizesi dahil olmak üzere benzersiz bir URL 'si olan her istek kendi önbelleğine sahip benzersiz bir varlık olarak değerlendirilir. Örneğin, `www.example.ashx?q=test1` için arka uçtan gelen yanıt ön kapı ortamında önbelleğe alınır ve aynı sorgu dizesine sahip sonraki önbellekler için döndürülür. `www.example.ashx?q=test2` isteği, kendi yaşam süresi ayarıyla ayrı bir varlık olarak önbelleğe alınır.

## <a name="cache-purge"></a>Önbellek Temizleme
Ön kapı, varlığın yaşam süresi (TTL) sona erene kadar varlıkları önbelleğe alacak. Varlığın TTL 'SI dolduktan sonra, bir istemci varlığı istediğinde, ön kapı ortamı, istemci isteğine yönelik yeni bir güncelleştirilmiş kopyasını alır ve mağaza önbelleği yeniler.
</br>Kullanıcılarınızın her zaman varlıklarınızın en son kopyasını elde ettiğinizden emin olmak için en iyi yöntem, varlıklarınızın her bir güncelleştirme için sürüm oluşturup yeni URL 'Ler olarak yayınlanmasına yöneliktir. Ön kapı, sonraki istemci istekleri için yeni varlıkları hemen alacak. Bazen önbelleğe alınmış içeriği tüm kenar düğümlerinden temizlemek ve bunları yeni güncelleştirilmiş varlıkları almaya zorlamak isteyebilirsiniz. Bunun nedeni, Web uygulamanızdaki güncelleştirmeler veya hatalı bilgiler içeren varlıkları hızlıca güncelleştirmeniz olabilir.

</br>Kenar düğümlerinden hangi varlıkları temizlemek istediğinizi seçin. Tüm varlıkları temizlemek istiyorsanız tümünü temizle onay kutusuna tıklayın. Aksi takdirde, yol metin kutusuna temizlemek istediğiniz her varlığın yolunu yazın. Aşağıdaki biçimler yolunda desteklenir.
1. **Tek URL Temizleme**: dosya UZANTıSıYLA tam URL 'yi belirterek tek bir varlığı temizleyin; Örneğin,/resim/Strasbourg.exe;
2. **Joker karakter Temizleme**: yıldız işareti (\*) joker karakter olarak kullanılabilir. Bir uç nokta altındaki tüm klasörleri, alt klasörleri ve dosyaları yolda/\* ve ardından/\*, örneğin,/resim/\*belirterek belirli bir klasör altındaki tüm alt klasörleri ve dosyaları temizleyin.
3. **Kök etki alanı temizleme**: yoldaki bitiş noktasının kökünü "/" ile temizleyin.

Ön kapıda önbellek temizler, büyük/küçük harfe duyarsızdır. Bunlara ek olarak, sorgu dizesi belirsiz olur, yani bir URL 'nin temizlenmesi, tüm sorgu dizesi çeşitlemelerini temizler. 

## <a name="cache-expiration"></a>Önbellek süre sonu
Bir öğenin önbellekte ne kadar süreyle depolanacağını anlamak için aşağıdaki üstbilgiler sırası kullanılır:</br>
1. Cache-Control: s-maxage =\<saniye >
2. Cache-Control: Max-Age =\<saniye >
3. Süre sonu: \<http-Date >

Yanıtın Cache-Control: Private, Cache-Control: No-Cache ve Cache-Control gibi önbelleğe alınacağını belirten Cache-Control yanıt üstbilgileri: No-Store kabul edilir. Ancak, aynı URL 'ye yönelik bir POP 'ta çok sayıda istek olduğunda, yanıt paylaşabilir. Cache-Control yoksa, varsayılan davranış, AFD 'ın X 'i 1 ila 3 gün arasında rastgele olarak seçtiğiniz X miktarı kadar önbelleğe alacak.


## <a name="request-headers"></a>İstek üst bilgileri

Önbelleğe alma kullanılırken aşağıdaki istek üst bilgileri bir arka uca iletilmeyecektir.
- Yetkilendirme
- İçerik uzunluğu
- Aktarım kodlaması

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.

---
title: 'Azure ön kapısı: önbelleğe alma'
description: Bu makale, Azure ön kapısı Standart/Premium 'un, önbelleğe alma özellikli yönlendirme kurallarıyla davranışını anlamanıza yardımcı olur.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 63ea252a4b4c673ae3028adb7ab793ac21fb2e99
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564594"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Azure ön kapı standardı/Premium ile önbelleğe alma (Önizleme)

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Bu makalede, ön kapı Standart/Premium (Önizleme) yollarının ve kural kümesinin önbelleğe alma özelliği etkinken nasıl davranacağını öğreneceksiniz. Azure ön kapısı, dinamik site hızlandırma ve yük dengeleme özellikli bir modern Content Delivery Network (CDN).

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="request-methods"></a>İstek yöntemleri

Yalnızca GET isteği yöntemi Azure ön kapıda önbelleğe alınmış içerik oluşturabilir. Tüm diğer istek yöntemleri her zaman ağ üzerinden dağıtılır.

## <a name="delivery-of-large-files"></a>Büyük dosyaların teslimi

Ön kapı Standart/Premium (Önizleme), dosya boyutunda bir Cap olmadan büyük dosyalar sunar. Ön kapı, nesne parçalama adlı bir teknik kullanır. Büyük bir dosya istendiğinde, ön kapı dosyanın kaynaktan daha küçük parçalarını alır. Tam veya bayt aralığı dosya isteği aldıktan sonra, ön kapı ortamı, dosyayı 8 MB öbekteki kaynaklardan ister.

Öbek, ön kapılı ortama ulaştıktan sonra önbelleğe alınır ve anında kullanıcıya sunulur. Ön kapı daha sonra bir sonraki öbeği paralel olarak önceden getirir. Bu ön alım, içeriğin kullanıcının önüne bir öbek kalmasını sağlar ve gecikme süresini azaltır. Bu işlem, tüm dosya indirilene (isteniyorsa) veya istemci bağlantıyı kapattığında devam eder.

Bayt aralığı isteği hakkında daha fazla bilgi için, [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html)' i okuyun.
Ön kapı, tüm dosyaları ön kapı önbelleğinde önbelleğe alınması gerekmeyen tüm öbekleri önbellekte önbelleğe alır. Dosya veya bayt aralıkları için istekleri önbellekten sunulur. Öbeklerin hepsi önbelleğe alınmamışsa, ön alma arka uca öbekleri istemek için kullanılır. Bu iyileştirme, bayt aralığı isteklerini destekleme yeteneğinin kaynağını kullanır. Kaynak, bayt aralığı isteklerini desteklemiyorsa, bu iyileştirme etkili olmaz.

## <a name="file-compression"></a>Dosya sıkıştırma

Azure ön kapısındaki dosyaları sıkıştırarak performansı geliştirme bölümüne bakın.

## <a name="query-string-behavior"></a>Sorgu dizesi davranışı

Ön kapıda, dosyaların sorgu dizesi içeren bir Web isteği için nasıl önbelleğe alınacağını denetleyebilirsiniz. Sorgu dizesi olan bir Web isteğinde, sorgu dizesi, isteğin bir soru işareti (?) sonrasında gerçekleşen bölümüdür. Sorgu dizesi bir veya daha fazla anahtar-değer çifti içerebilir; burada alan adı ve değeri bir eşittir işareti (=) ile ayrılmıştır. Her anahtar-değer çifti bir ve işareti (&) ile ayrılır. Örneğin, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Bir isteğin sorgu dizesinde birden fazla anahtar-değer çifti varsa, bunların sırası önemli değildir.

* **Sorgu dizelerini yoksay**: Bu modda, ön kapı Sorgu dizelerini istek sahibine ilk istekteki kaynağa geçirir ve varlığı önbelleğe alır. Ön kapı ortamından sunulan varlığın tüm istekleri, önbelleğe alınmış varlık sona erene kadar Sorgu dizelerini yoksayar.

* **Her benzersiz URL 'Yi önbelleğe al**: Bu modda, sorgu dizesi dahil olmak üzere benzersiz bir URL 'si olan her istek kendi önbelleğine sahip benzersiz bir varlık olarak değerlendirilir. Örneğin, için bir isteğin kaynağından gelen yanıt, `www.example.ashx?q=test1` ön kapı ortamında önbelleğe alınır ve aynı sorgu dizesiyle önbellekler için döndürülür. İçin bir istek `www.example.ashx?q=test2` , kendi yaşam süresi ayarıyla ayrı bir varlık olarak önbelleğe alınır.
* Ayrıca, önbellek anahtarı oluşturulduğunda, belirtilen parametreleri dahil etmek veya hariç tutmak için **önbellek anahtarı sorgu dizesi** davranışını belirtmek üzere kural kümesini de kullanabilirsiniz. Örneğin, varsayılan önbellek anahtarı:/kı/Image/asset.html ve örnek istek `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` . ' UserID ' sorgu dizesini dışlamak için bir kural kümesi kuralı var. Ardından sorgu dizesi önbellek anahtarı olur `/foo/image/asset.html?language=EN&sessionid=200` .

## <a name="cache-purge"></a>Önbellek Temizleme

Önbellek temizleme bölümüne bakın.

## <a name="cache-expiration"></a>Önbellek süre sonu
Bir öğenin önbellekte ne kadar süreyle depolanacağını anlamak için aşağıdaki üstbilgiler sırası kullanılır:</br>
1. Cache-Control: s-maxage =\<seconds>
2. Cache-Control: Max-Age =\<seconds>
3. Bitiminden \<http-date>

Yanıtın Cache-Control: Private, Cache-Control: No-Cache ve Cache-Control gibi önbelleğe alınmayabileceği belirten yanıt üstbilgileri Cache-Control: No-Store kabul edilir.  Cache-Control yoksa, varsayılan davranış ön kapısının kaynağı X süre için önbelleğe alacak olacaktır. Burada X, 1 ila 3 gün arasında rastgele bir şekilde seçilir.

## <a name="request-headers"></a>İstek üst bilgileri

Önbelleğe alma kullanılırken aşağıdaki istek üstbilgileri bir kaynağa iletilmez.
* İçerik Uzunluğu
* Transfer-Encoding

## <a name="cache-duration"></a>Önbellek süresi

Önbellek süresi, kural kümesinde yapılandırılabilir. Kural kümesi ile ayarlanan önbellek süresi, doğru bir önbellek geçersiz kılmalıdır. Yani, kaynak yanıt üst bilgisinin ne olduğuna bakılmaksızın geçersiz kılma değerini kullanacaktır.

## <a name="next-steps"></a>Sonraki adımlar

* [Kural kümesi eşleşme koşulları](concept-rule-set-match-conditions.md) hakkında daha fazla bilgi edinin
* [Kural kümesi eylemleri](concept-rule-set-actions.md) hakkında daha fazla bilgi edinin
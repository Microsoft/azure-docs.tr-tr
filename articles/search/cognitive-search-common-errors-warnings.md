---
title: Dizin Oluşturucu hataları ve uyarıları
titleSuffix: Azure Cognitive Search
description: Bu makalede, Azure Bilişsel Arama 'de AI zenginleştirme sırasında karşılaşabileceğiniz yaygın hatalara ve uyarılara yönelik bilgi ve çözümler sağlanmaktadır.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6b51581b5a8f94419dba60eee72669a3e1261b24
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151581"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Azure Bilişsel Arama ortak Dizin Oluşturucu hataları ve uyarıları sorunlarını giderme

Bu makalede, Azure Bilişsel Arama 'de dizin oluşturma ve AI zenginleştirmesi sırasında karşılaşabileceğiniz yaygın hatalara ve uyarılara yönelik bilgi ve çözümler sağlanmaktadır.

Hata sayısı [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)aştığında dizin oluşturma durduruluyor. 

Dizin oluşturucularının bu hataları yok saymasını (ve "başarısız belgeleri" atlamasını) istiyorsanız, `maxFailedItems` ve `maxFailedItemsPerBatch` [burada](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)açıklandığı gibi güncellemeyi göz önünde bulundurun.

> [!NOTE]
> Belge anahtarı (varsa) ile birlikte başarısız olan her belge, Dizin Oluşturucu yürütme durumundaki bir hata olarak görünür. [Dizin](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) oluşturucuyu, Dizin oluşturucularını hatalara karşı ayarlamanız durumunda daha sonraki bir noktada el ile karşıya yüklemek için kullanabilirsiniz.

Bu makaledeki hata bilgileri, dizin oluşturmanın devam etmesine izin veren hataları çözmenize yardımcı olabilir.

Uyarılar Dizin oluşturmayı durdurmaz, ancak beklenmedik sonuçlar oluşmasına neden olabilecek koşullar olduğunu gösterir. Eyleme ve senaryonuza bağlı olup olmadığına bakılmaksızın.

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Hata: belge okunamıyor

Dizin Oluşturucu, veri kaynağından belgeyi okuyamadı. Bunun nedeni aşağıdakiler olabilir:

| Neden | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| farklı belgeler genelinde tutarsız alan türleri | Değerin türü sütun türüyle eşleşmiyor. `'{47.6,-122.1}'` yazarlar sütununda depolanamadı.  Beklenen tür JArray. | Her alanın türünün farklı belgeler arasında aynı olduğundan emin olun. Örneğin, ilk belge `'startTime'` alanı bir tarih Tariheyse ve ikinci belgede bir dize ise, bu hata olur. |
| veri kaynağının temelindeki hizmetten alınan hatalar | (Cosmos DB) `{"Errors":["Request rate is large"]}` | Sağlıklı olduğundan emin olmak için depolama örneğinizi denetleyin. Ölçeklendirmeyi/bölümlemeyi ayarlamanız gerekebilir. |
| geçici sorunlar | Sunucudan sonuçlar alınırken aktarım düzeyi hatası oluştu. (sağlayıcı: TCP sağlayıcısı, hata: 0-var olan bir bağlantı uzak ana bilgisayar tarafından zorla kapatıldı | Bazen beklenmedik bağlantı sorunları var. Belgeyi Dizin oluşturucudan daha sonra tekrar çalıştırmayı deneyin. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-document-content"></a>Hata: belge içeriği ayıklanamadı
Blob veri kaynağı olan Dizin Oluşturucu, içeriği belgeden (örneğin, bir PDF dosyası) ayıklayamadı. Bunun nedeni aşağıdakiler olabilir:

| Neden | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| blob boyut sınırının üzerinde | Belge, geçerli hizmet katmanınız için belge ayıklama için en büyük boyut `'134217728'` baytı aşan `'150441598'` bayttır. | [blob dizin oluşturma hataları](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob desteklenmeyen içerik türüne sahip | Belge desteklenmeyen içerik türüne sahip `'image/png'` | [blob dizin oluşturma hataları](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob şifreli | Belge işlenemedi; şifrelenmiş veya parola korumalı olabilir. | Blobu [BLOB ayarlarına](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)atlayabilirsiniz. |
| geçici sorunlar | Blob işleme hatası: istek durduruldu: istek iptal edildi. | Bazen beklenmedik bağlantı sorunları var. Belgeyi Dizin oluşturucudan daha sonra tekrar çalıştırmayı deneyin. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Hata: belge ayrıştırılamadı
Dizin Oluşturucu veri kaynağından belgeyi okudu, ancak belge içeriği belirtilen alan eşleme şemasına dönüştürülürken bir sorun oluştu. Bunun nedeni aşağıdakiler olabilir:

| Neden | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Belge anahtarı eksik | Belge anahtarı eksik veya boş olamaz | Tüm belgelerin geçerli belge anahtarlarına sahip olduğundan emin olun |
| Belge anahtarı geçersiz | Belge anahtarı 1024 karakterden uzun olamaz | Belge anahtarını doğrulama gereksinimlerini karşılayacak şekilde değiştirin. |
| Alan eşlemesi bir alana uygulanamadı | `'functionName'` eşleme işlevi `'fieldName'`alana uygulanamadı. Dizi null olamaz. Parametre adı: bayt | Dizin oluşturucuda tanımlı [alan eşlemelerini](search-indexer-field-mappings.md) iki kez kontrol edin ve başarısız olan belgenin belirtilen alanındaki verilerle karşılaştırın. Alan eşlemelerini veya belge verilerini değiştirmek gerekebilir. |
| Alan değeri okunamadı | Dizin `'fieldIndex'``'fieldName'` sütunun değeri okunamadı. Sunucudan sonuçlar alınırken aktarım düzeyi hatası oluştu. (sağlayıcı: TCP sağlayıcısı, hata: 0-var olan bir bağlantı uzak ana bilgisayar tarafından zorla kapatıldı.) | Bu hatalar genellikle veri kaynağının temelindeki hizmet ile ilgili beklenmedik bağlantı sorunlarından kaynaklanır. Belgeyi Dizin oluşturucudan daha sonra tekrar çalıştırmayı deneyin. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Hata: yetenek yürütülemedi
Dizin Oluşturucu beceri içinde bir yetenek çalıştıramıyor.

| Neden | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Bir alan çok büyük bir terim içeriyor | Belgenizdeki bir terim [32 KB sınırından](search-limits-quotas-capacity.md#api-request-limits) daha büyük | Alanın filtrelenebilir, çok yönlü veya sıralanabilir olarak yapılandırılmadığından emin olmak için bu kısıtlamayı önleyebilirsiniz.
| Belge dizine eklenemeyecek kadar büyük | Belge, [en yüksek API istek boyutundan](search-limits-quotas-capacity.md#api-request-limits) daha büyük | [Büyük veri kümelerini dizin oluşturma](search-howto-large-index.md)
| Geçici bağlantı sorunları | Geçici bir hata oluştu. Lütfen daha sonra tekrar deneyin. | Bazen beklenmedik bağlantı sorunları var. Belgeyi Dizin oluşturucudan daha sonra tekrar çalıştırmayı deneyin. |
| Olası ürün hatası | Beklenmeyen bir hata oluştu. | Bu, bilinmeyen bir hata sınıfını gösterir ve bir ürün hatası olduğu anlamına gelebilir. Yardım almak için lütfen bir [destek bileti](https://ms.portal.azure.com/#create/Microsoft.Support) girin. |
| Bir beceri yürütme sırasında bir hatayla karşılaştı | (Birleştirme becerinizden) Bir veya daha fazla fark değeri geçersizdi ve ayrıştırılamadı. Metnin sonuna öğe eklendi | Sorunu giderecek hata iletisindeki bilgileri kullanın. Bu tür bir hata çözümü için eylem gerekir. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Hata: Web API isteği başarısız olduğu için yetenek yürütülemedi
Web API 'sine yapılan çağrı başarısız olduğundan yetenek yürütmesi başarısız oldu. Genellikle, bu hata sınıfı özel yetenekler kullanıldığında oluşur, bu durumda sorunu çözmek için özel kodunuzda hata ayıklaması yapmanız gerekir. Bunun yerine başarısızlık bir yerleşik beceriye ait ise, sorunu gidermeye yardımcı olması için hata iletisine bakın.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Hata: Web API 'SI yetenek yanıtı geçersiz olduğundan yetenek yürütülemedi
Web API çağrısı geçersiz bir yanıt döndürdüğünden yetenek yürütmesi başarısız oldu. Genellikle, bu hata sınıfı özel yetenekler kullanıldığında oluşur, bu durumda sorunu çözmek için özel kodunuzda hata ayıklaması yapmanız gerekir. Bunun yerine başarısızlık bir yerleşik beceriye ait ise, yardım almak için lütfen bir [destek bileti](https://ms.portal.azure.com/#create/Microsoft.Support) bildirin.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Hata: yetenek zaman sınırı içinde yürütülemedi
Bu hata iletisiyle karşılaşacağınız iki durum vardır; bunların her biri farklı bir şekilde ele alınmalıdır. Lütfen bu hatayı sizin için hangi beceriye döndürdiğine bağlı olarak aşağıdaki yönergeleri izleyin.

### <a name="built-in-cognitive-service-skills"></a>Yerleşik bilişsel hizmet becerileri
Dil algılama, varlık tanıma veya OCR gibi yerleşik bilişsel yeteneklerin çoğu bilişsel hizmet API uç noktası tarafından desteklenir. Bazen bu uç noktalarla geçici sorunlar vardır ve bir istek zaman aşımına uğrar. Geçici sorunlar için, beklemek ve yeniden denemek dışında bir çözüm yoktur. Risk azaltma olarak, Dizin Oluşturucularınızı [bir zamanlamaya göre çalışacak](search-howto-schedule-indexers.md)şekilde ayarlamayı düşünün. Zamanlanan dizin oluşturma, sol taraftaki yeri seçer. Geçici sorunların çözümlendiğini, dizin oluşturma ve Bilişsel Beceri işleme, bir sonraki zamanlanan çalıştırmaya devam edebilmelidir.

### <a name="custom-skills"></a>Özel beceriler
Oluşturduğunuz özel bir yeteneğe sahip bir zaman aşımı hatasıyla karşılaşırsanız, deneyebileceğiniz birkaç işlem vardır. İlk olarak, özel becerinizi gözden geçirin ve sonsuz bir döngüde takılı olmadığından ve sürekli olarak bir sonuç döndürmediğinden emin olun. Durum olduğunu onayladıktan sonra, becerinizde yürütme zamanının ne olduğunu saptayın. Özel beceri tanımınızda açıkça bir `timeout` değeri ayarlamadıysanız, varsayılan `timeout` 30 saniyedir. Becerinizi yürütmek için 30 saniye yeterince uzun değilse, özel beceri tanımınızda daha yüksek bir `timeout` değeri belirtebilirsiniz. Zaman aşımının 90 saniyeye ayarlandığı özel beceri tanımına bir örnek aşağıda verilmiştir:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

`timeout` parametresi için ayarlayabileceğiniz maksimum değer 230 saniyedir.  Özel becerinizde sürekli olarak 230 saniye içinde yürütülemediğinde, tek bir yürütmede işlemek üzere daha az belge olması için özel yeteneğin `batchSize` azaltmayı düşünebilirsiniz.  `batchSize` zaten 1 olarak ayarladıysanız, yeteneği 230 saniye altında yürütebilmek için yeniden yazmanız gerekir veya herhangi bir özel beceri için yürütme süresi en fazla 230 saniyelik olacak şekilde birden çok özel beceriye bölebilirsiniz. Daha fazla bilgi için [özel beceri belgelerini](cognitive-search-custom-skill-web-api.md) gözden geçirin.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Hata: '`MergeOrUpload`' | arama dizinine '`Delete`' belgesi

Belge okundu ve işlendi, ancak Dizin Oluşturucu onu arama dizinine ekleyemedi. Bunun nedeni aşağıdakiler olabilir:

| Neden | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Belgenizdeki bir terim [32 KB sınırından](search-limits-quotas-capacity.md#api-request-limits) daha büyük | Bir alan çok büyük bir terim içeriyor | Alanın filtrelenebilir, çok yönlü veya sıralanabilir olarak yapılandırılmadığından emin olmak için bu kısıtlamayı önleyebilirsiniz.
| Belge, [en yüksek API istek boyutundan](search-limits-quotas-capacity.md#api-request-limits) daha büyük | Belge dizine eklenemeyecek kadar büyük | [Büyük veri kümelerini dizin oluşturma](search-howto-large-index.md)
| Belge koleksiyonda çok fazla nesne içeriyor | Belgenizdeki bir koleksiyon [tüm karmaşık koleksiyonlar limitindeki en fazla öğeyi](search-limits-quotas-capacity.md#index-limits) aşıyor | Belgedeki karmaşık toplamanın boyutunu sınırın altına düşürmenizi ve yüksek depolama kullanımından kaçınmanızı öneririz.
| Hizmet, sorgulama veya dizin oluşturma gibi başka bir yük altında olduğundan, hedef dizine bağlanma sorunu (yeniden denemeden sonra devam eden). | Güncelleştirme diziniyle bağlantı kurulamadı. Arama hizmeti ağır yük altında. | [Arama hizmetinizin ölçeğini artırma](search-capacity-planning.md)
| Arama hizmeti 'nin hizmet güncelleştirmesi için düzeltme eki uygulanıyor veya bir topoloji yeniden yapılandırması ortasında. | Güncelleştirme diziniyle bağlantı kurulamadı. Arama hizmeti şu anda açık/arama hizmeti bir geçiş işlemi yaşıyor. | [SLA belgeleri](https://azure.microsoft.com/support/legal/sla/search/v1_0/) başına% 99,9 kullanılabilirlik için en az 3 çoğaltmalarla hizmeti yapılandırın
| Temeldeki işlem/ağ kaynağında hata (nadir) | Güncelleştirme diziniyle bağlantı kurulamadı. Bilinmeyen bir hata oluştu. | Başarısız bir durumdan almak için [bir zamanlamaya göre çalıştırılacak](search-howto-schedule-indexers.md) Dizin oluşturucularını yapılandırın.
| Ağ sorunları nedeniyle bir zaman aşımı süresi içinde hedef dizine yapılan bir dizin oluşturma isteği onaylanmadı. | Arama diziniyle zamanında bağlantı kurulamadı. | Başarısız bir durumdan almak için [bir zamanlamaya göre çalıştırılacak](search-howto-schedule-indexers.md) Dizin oluşturucularını yapılandırın. Ayrıca, bu hata durumu devam ederse Dizin Oluşturucu [toplu iş boyutunu](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) azaltmayı deneyin.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Hata: dizine eklenecek Dizin Oluşturucu verileri geçersiz olduğundan belge dizini oluşturulamadı

Belge okundu ve işlendi, ancak dizin alanlarının yapılandırmasındaki bir uyumsuzluk ve Dizin Oluşturucu tarafından ayıklanan verilerin doğası nedeniyle arama dizinine eklenemedi. Bunun nedeni aşağıdakiler olabilir:

| Neden | Ayrıntılar/örnek
| --- | ---
| Dizin Oluşturucu tarafından ayıklanan alanların veri türü, karşılık gelen hedef dizin alanının veri modeliyle uyumsuz. | '_Data_' anahtarına sahip belgedeki '_Data_' veri alanı ' Edm. String ' ' türünde geçersiz bir değere sahip. Beklenen tür ' Collection (EDM. String) ' idi. |
| Dize değerinden herhangi bir JSON varlığı ayıklanamadı. | '_Data_' alanının ' Edm. String ' ' DEĞERI bir JSON nesnesi olarak ayrıştırılamadı. Hata: ' bir değer ayrıştırdıktan sonra beklenmeyen bir karakterle karşılaşıldı: ' '. Yol '_yol_', satır 1, konum 3162. ' |
| JSON varlıklarının bir koleksiyonu bir dize değerinden ayıklanamadı.  | '_Data_' alanının ' Edm. String ' ' DEĞERI bir JSON dizisi olarak ayrıştırılamadı. Hata: ' bir değer ayrıştırdıktan sonra beklenmeyen bir karakterle karşılaşıldı: ' '. Yol ' [0] ', satır 1, konum 27. ' |
| Kaynak belgede bilinmeyen bir tür bulundu. | Bilinmeyen '_bilinmiyor_' türünün dizini oluşturulamaz |
| Kaynak belgede Coğrafya noktaları için uyumsuz bir gösterim kullanıldı. | WKT noktası dize sabit değerleri desteklenmiyor. Lütfen bunun yerine GeoJson nokta sabit değerlerini kullanın |

Tüm bu durumlarda, Dizin şemasını doğru bir şekilde oluşturup uygun [Dizin Oluşturucu alan eşlemelerini](search-indexer-field-mappings.md)ayarlamış olduğunuzdan emin olmak için, Dizin oluşturucular Için [desteklenen veri türleri](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) ve [veri türü eşlemesi](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) ' ne bakın. Hata iletisi, uyuşmazlığın kaynağını izlemeye yardımcı olabilecek ayrıntıları içerir.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Hata: Dizin Oluşturucu en fazla çalışma süresi içinde belge işlenemedi

Dizin Oluşturucu, izin verilen yürütme süresi içinde veri kaynağından tek bir belgeyi işlemeyi tamamlayamediğinde bu hata oluşur. Becerileri kullanıldığında [en fazla çalışma süresi](search-limits-quotas-capacity.md#indexer-limits) daha kısadır. Bu hata oluştuğunda, maxFailedItems 0 dışında bir değere ayarladıysanız Dizin Oluşturucu, dizin oluşturmanın ilerlemesinin devam edebilmesi için gelecekteki çalışmadaki belgeyi atlar. Herhangi bir belgeyi atlamak için kabul ediyorsanız veya bu hatayı sürekli olarak görüyorsanız, tek bir Dizin Oluşturucu yürütmesi içinde kısmi ilerleme yapılabilmesi için belgeleri daha küçük belgelere bozmayı düşünün.

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-could-not-execute-skill-because-a-skill-input-was-invalid"></a>Uyarı: bir yetenek girişi geçersiz olduğundan yetenek yürütülemedi
Niteliğin bir girişi eksik, yanlış türde veya geçersiz olduğundan Dizin Oluşturucu beceri içinde bir yetenek çalıştıramıyor.

Bilişsel yetenekler için gerekli girişler ve isteğe bağlı girişler vardır. Örneğin, [anahtar tümceciği ayıklama becerinin](cognitive-search-skill-keyphrases.md) iki gerekli girişi vardır `text`, `languageCode`ve isteğe bağlı giriş yoktur. Gerekli girişler varsa, Beceri atlanır ve bir uyarı oluşturur. Atlanan yetenekler hiçbir çıkış oluşturmaz, bu nedenle diğer yetenekler atlanan yeteneğin çıkışlarını kullanıyorsa, ek uyarılar oluşturabilir.

Eksik giriş durumunda varsayılan bir değer sağlamak istiyorsanız, [koşullu yeteneği](cognitive-search-skill-conditional.md) kullanarak varsayılan bir değer oluşturabilir ve sonra [koşullu yeteneğin](cognitive-search-skill-conditional.md) çıkışını yetenek girişi olarak kullanabilirsiniz.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Neden | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Beceri girişi yanlış türde | Gerekli yetenek girişi `X` `String`beklenen türde değil. Gerekli yetenek girişi `X` beklenen biçimde değil. | Bazı yetenekler belirli türlerin girdilerini bekler, örneğin yaklaşım [becerisi](cognitive-search-skill-sentiment.md) `text` bir dize olmasını bekler. Giriş dize olmayan bir değer belirtiyorsa, yetenek yürütülmez ve çıkış oluşturmaz. Veri ayarlamış olduğunuz giriş değerlerinin türünde Tekdüzen olduğundan emin olun veya girişi önceden işlemek için [özel bir Web API 'si](cognitive-search-custom-skill-web-api.md) kullanın. Yeteneği bir dizi üzerinden yineleyorsanız, yetenek bağlamını ve girişin doğru konumlarda `*` olduğunu kontrol edin. Genellikle bağlam ve giriş kaynağı diziler için `*` bitmelidir. |
| Yetenek girişi eksik | Gerekli yetenek girişi `X` eksik. | Tüm belgeleriniz bu uyarıyı alıyorsa, büyük olasılıkla giriş yollarında bir yazım hatası vardır ve özellik adının büyük küçük harf, ek veya eksik `*` ve veri kaynağından alınan belgeler gerekli girdileri tanımlar. |
| Yetenek dil kodu girişi geçersiz | Beceri girişi `languageCode`, en az biri geçersiz olan `X,Y,Z`dil kodlarını içerir. | Daha fazla ayrıntı için [aşağıya](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) bakın |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Uyarı: ' languageCode ' yetenek girişi, en az bir tane geçersiz olan ' X, Y, Z ' dil kodlarına sahip.
Aşağı akış beceriye yönelik isteğe bağlı `languageCode` girişi için geçirilen bir veya daha fazla değer desteklenmiyor. Bu durum, [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) çıktısını sonraki becerilerle geçirmeniz durumunda meydana gelir ve çıktı bu aşağı akış becerilerinizi desteklenenden daha fazla dilde oluşur.

Veri ayarlamış olduğunuz tek bir dilde olduğunu biliyorsanız, [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) ve `languageCode` yetenek girişini kaldırmalı ve bu beceri için dilin desteklendiğini varsayarak bunun yerine bu beceri için `defaultLanguageCode` yetenek parametresini kullanmanız gerekir.

Veri ayarlamış olduğunuz dilin birden çok dil içerdiğini ve bu nedenle [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) ve `languageCode` girdisine ihtiyacınız olduğunu biliyorsanız, şunu içine geçirmeden önce desteklenmeyen dillerle metni filtrelemek Için bir [conditionalbeceri](cognitive-search-skill-conditional.md) eklemeyi göz önünde bulundurun. aşağı akış beceriye metin.  İşte bunun Entityrecognitionbeceri için nasıl görünebileceğini bir örnek:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Bu hata iletisini oluşturabilecek yeteneklerin her biri için şu anda desteklenen dillere yönelik bazı başvurular aşağıda verilmiştir:
* [Desteklenen metin analizi diller](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) ( [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [Entityrecognitionbeceri](cognitive-search-skill-entity-recognition.md)ve [sentimentbecerisi](cognitive-search-skill-sentiment.md)için)
* [Translator tarafından desteklenen diller](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) ( [metin Için translationbeceri](cognitive-search-skill-text-translation.md)için)
* [Metin bölünmüş beceri](cognitive-search-skill-textsplit.md) Desteklenen Diller: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Uyarı: yetenek girişi kesildi
Bilişsel yetenekler, bir kerede çözümlenebilecek metin uzunluğuna yönelik sınırlara sahiptir. Bu yeteneklerin metin girişi bu sınırın üzerinde ise, sınırı karşılamak için metni kısaltacak ve sonra bu kesilen metinde zenginleştirme gerçekleştiririz. Bu, yeteneğin yürütüldüğü, ancak tüm verilerinizin üzerinde olmadığı anlamına gelir.

Aşağıdaki örnekte, `'text'` giriş alanı karakter sınırının üzerinde olursa bu uyarıyı tetikleyebilir. Beceri girişi sınırlarını [beceriler belgelerinde](cognitive-search-predefined-skills.md)bulabilirsiniz.

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Tüm metinlerin çözümlendiğinden emin olmak istiyorsanız, [bölünmüş yeteneği](cognitive-search-skill-textsplit.md)kullanmayı göz önünde bulundurun.

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Uyarı: Web API 'SI yetenek yanıtı uyarıları içeriyor
Dizin Oluşturucu beceri içinde bir yetenek çalıştırabiliyor, ancak Web API isteğinden alınan yanıt yürütme sırasında uyarılar olduğunu belirtti. Verilerinizin nasıl etkilendiğini ve eylem gerekip gerekmediğini anlamak için uyarıları gözden geçirin.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Uyarı: geçerli dizin oluşturucu yapılandırması artımlı ilerlemeyi desteklemiyor

Bu uyarı yalnızca Cosmos DB veri kaynakları için oluşur.

Dizin oluşturma sırasında artımlı ilerleme durumu, Dizin Oluşturucu yürütmesi geçici hatalara veya yürütme süresi sınırına göre kesintiye uğrarsa, dizin oluşturucunun, sıfırdan tüm koleksiyonu yeniden dizinlemesini sağlamak yerine, bir sonraki çalıştırıldığında kaldığınız yerden seçim yapmasına olanak sağlar. Büyük koleksiyonlar dizinlenirken bu özellikle önemlidir.

Tamamlanmamış bir dizin oluşturma işinin sürdürülebilmesi, `_ts` sütununa göre sıralanmış belgeler elde edilebilir. Dizin Oluşturucu, sonraki hangi belgenin çalıştırılacağını belirleyen zaman damgasını kullanır. `_ts` sütunu eksikse veya Dizin Oluşturucu özel bir sorgunun bu tarafından sıralı olup olmadığını belirleyeemdi, Dizin Oluşturucu başlangıcında başlar ve bu uyarıyı görürsünüz.

Bu davranışı geçersiz kılmak, artımlı ilerleme durumunu etkinleştirmek ve `assumeOrderByHighWatermarkColumn` yapılandırma özelliği kullanılarak bu uyarının gizlenmesi mümkündür.

Daha fazla bilgi için bkz. [artımlı ilerleme ve özel sorgular](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Uyarı: projeksiyon sırasında bazı veriler kayboldu. ' Y ' tablosundaki ' X ' satırı, çok uzun olan ' Z ' dize özelliğine sahip.

[Tablo depolama hizmeti](https://azure.microsoft.com/services/storage/tables) , büyük [varlık özelliklerinin](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) nasıl kullanılabileceğini sınırlandırır. Dizelerde 32.000 karakter veya daha az olabilir. Dize özelliği 32.000 karakterden daha uzun bir satır yansıtılmakta ise, yalnızca ilk 32.000 karakter korunur. Bu sorunu geçici olarak çözmek için 32.000 karakterden uzun dize özellikleriyle satırları yansıtımaktan kaçının.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Uyarı: ayıklanan metin X karakter olarak kesildi
Dizin oluşturucular, herhangi bir belgeden ne kadar metin ayıklanabileceği sayısını sınırlar. Bu sınır, fiyatlandırma katmanına bağlıdır: ücretsiz katman için 32.000 karakter, temel için 64.000 ve standart, Standart S2 ve Standart S3 katmanları için 4.000.000. Kesilen metnin dizini oluşturulmaz. Bu uyarıyı önlemek için, büyük miktarlarda metinle birlikte belgeleri birden çok, daha küçük belgeye bölmek için kullanmayı deneyin. 

Daha fazla bilgi için bkz. [Dizin Oluşturucu sınırları](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Uyarı: ' X ' Çıkış alanı arama dizinine eşlenemiyor
Varolmayan/null olmayan verilere başvuran çıkış alanı eşlemeleri, her belge için uyarı üretir ve boş bir dizin alanına neden olur. Bu soruna geçici bir çözüm olarak, olası yazım hataları için çıkış alan eşleme kaynak yollarını iki kez kontrol edin veya [koşullu beceriye](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)kullanarak varsayılan bir değer ayarlayın.

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Uyarı: veri değişikliği algılama ilkesi ' X ' anahtar sütununu kullanacak şekilde yapılandırıldı
[Veri değişikliği algılama ilkeleri](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) , değişikliği algılamak için kullandıkları sütunlar için özel gereksinimlere sahiptir. Bu gereksinimlerden biri, kaynak öğe her değiştirildiğinde bu sütunun Güncellenme öğesidir. Başka bir gereksinim, bu sütun için yeni değerin önceki değerden daha büyük olması. Anahtar sütunlar, her güncelleştirmede değişmediği için bu gereksinimi yerine getirmiyor. Bu sorunu geçici olarak çözmek için değişiklik algılama ilkesi için farklı bir sütun seçin.
---
title: Dizin oluşturucu hataları ve uyarıları
titleSuffix: Azure Cognitive Search
description: Bu makalede, Azure Bilişsel Arama 'de AI zenginleştirme sırasında karşılaşabileceğiniz yaygın hatalara ve uyarılara yönelik bilgi ve çözümler sağlanmaktadır.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3ba0abe8510291351c10ba085ba7e42b8197d886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553247"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Azure Bilişsel Arama ortak Dizin Oluşturucu hataları ve uyarıları sorunlarını giderme

Bu makalede, Azure Bilişsel Arama 'de dizin oluşturma ve AI zenginleştirmesi sırasında karşılaşabileceğiniz yaygın hatalara ve uyarılara yönelik bilgi ve çözümler sağlanmaktadır.

Hata sayısı [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)aştığında dizin oluşturma durduruluyor. 

Dizin oluşturucularının bu hataları yok saymasını (ve "başarısız belgeleri" atlamasını) istiyorsanız, `maxFailedItems` `maxFailedItemsPerBatch` [burada](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)açıklandığı gibi ve ' yi güncellemeyi göz önünde bulundurun.

> [!NOTE]
> Belge anahtarı (varsa) ile birlikte başarısız olan her belge, Dizin Oluşturucu yürütme durumundaki bir hata olarak görünür. [Dizin](/rest/api/searchservice/addupdate-or-delete-documents) oluşturucuyu, Dizin oluşturucularını hatalara karşı ayarlamanız durumunda daha sonraki bir noktada el ile karşıya yüklemek için kullanabilirsiniz.

Bu makaledeki hata bilgileri, dizin oluşturmanın devam etmesine izin veren hataları çözmenize yardımcı olabilir.

Uyarılar Dizin oluşturmayı durdurmaz, ancak beklenmedik sonuçlar oluşmasına neden olabilecek koşullar olduğunu gösterir. Eyleme ve senaryonuza bağlı olup olmadığına bakılmaksızın.

API sürümünden itibaren `2019-05-06` , öğe düzeyinde Dizin Oluşturucu hataları ve uyarıları, nedenler ve sonraki adımlarda daha fazla açıklık sağlamak üzere yapılandırılır. Bunlar aşağıdaki özellikleri içerir:

| Özellik | Açıklama | Örnek |
| --- | --- | --- |
| anahtar | Hatanın veya uyarıdan etkilenen belge KIMLIĞI. | https: \/ /coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | Hatanın veya uyarının nerede oluştuğunu açıklayan işlem adı. Bu, şu yapı tarafından oluşturulur: [Kategori]. [alt kategori]. [resourceType]. Kaynak | Belgetextraction. azureblob. myBlobContainerName Enrichment. WebApiSkill. mySkillName Projection. Searchındex. OutputFieldMapping. mbir Putfieldname Projection. Searchındex. MergeOrUpload. Myındexname Projection. KnowledgeStore. Table. myTableName |
| message | Hatanın veya uyarının üst düzey bir açıklaması. | Web API isteği başarısız olduğundan yetenek yürütülemedi. |
| bilgileri | Özel bir yetenek yürütülerek WebApi yanıtı gibi sorunu tanılamaya yardımcı olabilecek ek ayrıntılar başarısız oldu. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 kaynak, Func `2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` ... yığın izlemenin geri kalanı... |
| Belgetationlink | Hata ayıklama ve sorunu çözme hakkında ayrıntılı bilgiler içeren ilgili belgelere bir bağlantı. Bu bağlantı genellikle bu sayfada aşağıdaki bölümlerden birini işaret eder. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>Hata: belge okunamıyor

Dizin Oluşturucu, veri kaynağından belgeyi okuyamadı. Bunun nedeni aşağıdakiler olabilir:

| Nedeni | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Farklı belgeler genelinde tutarsız alan türleri | "Değer türü sütun türüyle eşleşmiyor. `'{47.6,-122.1}'`Yazarlar sütununda depolanamadı.  Beklenen tür JArray. "  "Veri türü nvarchar, float olarak dönüştürülürken hata oluştu."  "' 12 ay ' nvarchar değeri int veri türüne dönüştürülürken dönüştürme başarısız oldu."  "İfade, int veri türüne dönüştürülürken aritmetik taşma hatası." | Her alanın türünün farklı belgeler arasında aynı olduğundan emin olun. Örneğin, ilk belge `'startTime'` alanı bir tarih saat ise ve ikinci belgede bir dize ise, bu hata olur. |
| veri kaynağının temelindeki hizmetten alınan hatalar | (Cosmos DB) `{"Errors":["Request rate is large"]}` | Sağlıklı olduğundan emin olmak için depolama örneğinizi denetleyin. Ölçeklendirmeyi/bölümlemeyi ayarlamanız gerekebilir. |
| geçici sorunlar | Sunucudan sonuçlar alınırken aktarım düzeyi hatası oluştu. (sağlayıcı: TCP sağlayıcısı, hata: 0-var olan bir bağlantı uzak ana bilgisayar tarafından zorla kapatıldı | Bazen beklenmedik bağlantı sorunları var. Belgeyi Dizin oluşturucudan daha sonra tekrar çalıştırmayı deneyin. |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Hata: belgenizdeki içerik veya meta veriler ayıklanamadı
Blob veri kaynağı olan Dizin Oluşturucu, belgeden (örneğin, bir PDF dosyası) içerik veya meta verileri ayıklayamadı. Bunun nedeni aşağıdakiler olabilir:

| Nedeni | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| blob boyut sınırının üzerinde | Belge, `'150441598'` `'134217728'` geçerli hizmet katmanınız için belge ayıklama için maksimum boyut baytlarını aşan bayttır. | [blob dizin oluşturma hataları](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| blob desteklenmeyen içerik türüne sahip | Belgede desteklenmeyen içerik türü yok `'image/png'` | [blob dizin oluşturma hataları](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| blob şifreli | Belge işlenemedi; şifrelenmiş veya parola korumalı olabilir. | Blobu [BLOB ayarlarına](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)atlayabilirsiniz. |
| geçici sorunlar | "Blob işleme hatası: istek durduruldu: istek iptal edildi." "İşlem sırasında belge zaman aşımına uğradı." | Bazen beklenmedik bağlantı sorunları var. Belgeyi Dizin oluşturucudan daha sonra tekrar çalıştırmayı deneyin. |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>Hata: belge ayrıştırılamadı
Dizin Oluşturucu veri kaynağından belgeyi okudu, ancak belge içeriği belirtilen alan eşleme şemasına dönüştürülürken bir sorun oluştu. Bunun nedeni aşağıdakiler olabilir:

| Nedeni | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Belge anahtarı eksik | Belge anahtarı eksik veya boş olamaz | Tüm belgelerin geçerli belge anahtarlarına sahip olduğundan emin olun. Belge anahtarı, [Dizin tanımının](/rest/api/searchservice/create-index#request-body)bir parçası olarak ' Key ' özelliği ayarlanarak belirlenir. ' Key ' olarak işaretlenen özellik belirli bir belgede bulunamazsa, Dizin oluşturucular bu hatayı alır. |
| Belge anahtarı geçersiz | Belge anahtarı 1024 karakterden uzun olamaz | Belge anahtarını doğrulama gereksinimlerini karşılayacak şekilde değiştirin. |
| Alan eşlemesi bir alana uygulanamadı | Eşleme işlevi `'functionName'` alana uygulanamadı `'fieldName'` . Dizi null olamaz. Parametre adı: bayt | Dizin oluşturucuda tanımlı [alan eşlemelerini](search-indexer-field-mappings.md) iki kez kontrol edin ve başarısız olan belgenin belirtilen alanındaki verilerle karşılaştırın. Alan eşlemelerini veya belge verilerini değiştirmek gerekebilir. |
| Alan değeri okunamadı | Dizindeki sütunun değeri okunamadı `'fieldName'` `'fieldIndex'` . Sunucudan sonuçlar alınırken aktarım düzeyi hatası oluştu. (sağlayıcı: TCP sağlayıcısı, hata: 0-var olan bir bağlantı uzak ana bilgisayar tarafından zorla kapatıldı.) | Bu hatalar genellikle veri kaynağının temelindeki hizmet ile ilgili beklenmedik bağlantı sorunlarından kaynaklanır. Belgeyi Dizin oluşturucudan daha sonra tekrar çalıştırmayı deneyin. |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>Hata: ' ' `xyz` eşleme işlevi uygulanırken seri durumundan çıkarma sorunu nedeniyle ' ' Çıkış alanı arama dizinine eşlenemiyor `abc`
Çıkış verileri, kullanmakta olduğunuz eşleme işlevi için yanlış biçimde olduğundan çıkış eşlemesi başarısız olmuş olabilir. Örneğin, ikili verilerde Base64Encode Mapping işlevinin uygulanması bu hatayı oluşturur. Sorunu çözmek için eşleme işlevini belirtmeden Dizin oluşturucuyu yeniden çalıştırın veya eşleme işlevinin çıktı alanı veri türüyle uyumlu olduğundan emin olun. Ayrıntılar için bkz. [çıktı alan eşleme](cognitive-search-output-field-mapping.md) .

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>Hata: yetenek yürütülemedi
Dizin Oluşturucu beceri içinde bir yetenek çalıştıramıyor.

| Nedeni | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Geçici bağlantı sorunları | Geçici bir hata oluştu. Lütfen daha sonra yeniden deneyin. | Bazen beklenmedik bağlantı sorunları var. Belgeyi Dizin oluşturucudan daha sonra tekrar çalıştırmayı deneyin. |
| Olası ürün hatası | Beklenmeyen bir hata oluştu. | Bu, bilinmeyen bir hata sınıfını gösterir ve bir ürün hatası olduğu anlamına gelebilir. Yardım almak için lütfen bir [destek bileti](https://ms.portal.azure.com/#create/Microsoft.Support) girin. |
| Bir beceri yürütme sırasında bir hatayla karşılaştı | (Birleştirme becerinizden) Bir veya daha fazla fark değeri geçersizdi ve ayrıştırılamadı. Metnin sonuna öğe eklendi | Sorunu giderecek hata iletisindeki bilgileri kullanın. Bu tür bir hata çözümü için eylem gerekir. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Hata: Web API isteği başarısız olduğu için yetenek yürütülemedi
Web API 'sine yapılan çağrı başarısız olduğundan yetenek yürütmesi başarısız oldu. Genellikle, bu hata sınıfı özel yetenekler kullanıldığında oluşur, bu durumda sorunu çözmek için özel kodunuzda hata ayıklaması yapmanız gerekir. Bunun yerine başarısızlık bir yerleşik beceriye ait ise, sorunu gidermeye yardımcı olması için hata iletisine bakın.

Bu sorunu ayıklarken, bu beceri için herhangi bir [yetenek girişi uyarısına](#warning-skill-input-was-invalid) dikkat edin. Web API uç noktanız, Dizin Oluşturucu beklenmeyen bir girdi geçirdiğinden başarısız olabilir.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Hata: Web API 'SI yetenek yanıtı geçersiz olduğundan yetenek yürütülemedi
Web API çağrısı geçersiz bir yanıt döndürdüğünden yetenek yürütmesi başarısız oldu. Genellikle, bu hata sınıfı özel yetenekler kullanıldığında oluşur, bu durumda sorunu çözmek için özel kodunuzda hata ayıklaması yapmanız gerekir. Bunun yerine başarısızlık bir yerleşik beceriye ait ise, yardım almak için lütfen bir [destek bileti](https://ms.portal.azure.com/#create/Microsoft.Support) bildirin.

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Hata: yetenek zaman sınırı içinde yürütülemedi
Bu hata iletisiyle karşılaşacağınız iki durum vardır; bunların her biri farklı bir şekilde ele alınmalıdır. Lütfen bu hatayı sizin için hangi beceriye döndürdiğine bağlı olarak aşağıdaki yönergeleri izleyin.

### <a name="built-in-cognitive-service-skills"></a>Yerleşik bilişsel hizmet becerileri
Dil algılama, varlık tanıma veya OCR gibi yerleşik bilişsel yeteneklerin çoğu bilişsel hizmet API uç noktası tarafından desteklenir. Bazen bu uç noktalarla geçici sorunlar vardır ve bir istek zaman aşımına uğrar. Geçici sorunlar için, beklemek ve yeniden denemek dışında bir çözüm yoktur. Risk azaltma olarak, Dizin Oluşturucularınızı [bir zamanlamaya göre çalışacak](search-howto-schedule-indexers.md)şekilde ayarlamayı düşünün. Zamanlanan dizin oluşturma, sol taraftaki yeri seçer. Geçici sorunların çözümlendiğini, dizin oluşturma ve Bilişsel Beceri işleme, bir sonraki zamanlanan çalıştırmaya devam edebilmelidir.

Bu hatayı, yerleşik bir Bilişsel Beceri yeteneği için aynı belgede görmeye devam ederseniz, bu beklenmediğinden yardım almak için lütfen bir [destek bileti](https://ms.portal.azure.com/#create/Microsoft.Support) dosyası sağlayın.

### <a name="custom-skills"></a>Özel beceriler
Oluşturduğunuz özel bir yeteneğe sahip bir zaman aşımı hatasıyla karşılaşırsanız, deneyebileceğiniz birkaç işlem vardır. İlk olarak, özel becerinizi gözden geçirin ve sonsuz bir döngüde takılı olmadığından ve sürekli olarak bir sonuç döndürmediğinden emin olun. Durum olduğunu onayladıktan sonra, becerinizde yürütme zamanının ne olduğunu saptayın. `timeout`Özel beceri tanımınızda açık bir değer ayarlamadıysanız varsayılan değer `timeout` 30 saniyedir. Becerinizi yürütmek için 30 saniye yeterince uzun değilse, `timeout` özel beceri tanımınızda daha yüksek bir değer belirtebilirsiniz. Zaman aşımının 90 saniyeye ayarlandığı özel beceri tanımına bir örnek aşağıda verilmiştir:

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

Parametresi için ayarlayabileceğiniz maksimum değer `timeout` 230 saniyedir.  Özel becerinizde sürekli olarak 230 saniye içinde yürütülemediğinde, `batchSize` tek bir yürütmede işlemek üzere daha az belge olması için özel becerinizi azaltmayı düşünebilirsiniz.  `batchSize`' Yi zaten 1 olarak ayarladıysanız, bu yeteneği 230 saniyelik bir süre içinde yürütebilmek için yeniden yazmanız gerekir veya herhangi bir özel beceri için yürütme süresi en fazla 230 saniyelik olacak şekilde birden çok özel beceriye bölebilirsiniz. Daha fazla bilgi için [özel beceri belgelerini](cognitive-search-custom-skill-web-api.md) gözden geçirin.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Hata: ' `MergeOrUpload` ' | `Delete`arama dizinine ' ' belgesi

Belge okundu ve işlendi, ancak Dizin Oluşturucu onu arama dizinine ekleyemedi. Bunun nedeni aşağıdakiler olabilir:

| Nedeni | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Bir alan çok büyük bir terim içeriyor | Belgenizdeki bir terim [32 KB sınırından](search-limits-quotas-capacity.md#api-request-limits) daha büyük | Alanın filtrelenebilir, çok yönlü veya sıralanabilir olarak yapılandırılmadığından emin olmak için bu kısıtlamayı önleyebilirsiniz.
| Belge dizine eklenemeyecek kadar büyük | Belge, [en yüksek API istek boyutundan](search-limits-quotas-capacity.md#api-request-limits) daha büyük | [Büyük veri kümelerini dizin oluşturma](search-howto-large-index.md)
| Belge koleksiyonda çok fazla nesne içeriyor | Belgenizdeki bir koleksiyon [tüm karmaşık koleksiyonlar sınırında bulunan en fazla öğe sayısını](search-limits-quotas-capacity.md#index-limits) aşıyor " `'1000052'` `'4303'` anahtardaki belge koleksiyonlardaki nesneler içeriyor (JSON dizileri). Çoğu `'3000'` nesnenin tüm belge genelinde koleksiyonlar halinde olmasına izin verilir. Lütfen nesneleri koleksiyonlardan kaldırın ve belgeyi yeniden dizinlemeyi deneyin. " | Belgedeki karmaşık toplamanın boyutunu sınırın altına düşürmenizi ve yüksek depolama kullanımından kaçınmanızı öneririz.
| Hizmet, sorgulama veya dizin oluşturma gibi başka bir yük altında olduğundan, hedef dizine bağlanma sorunu (yeniden denemeden sonra devam eden). | Güncelleştirme diziniyle bağlantı kurulamadı. Arama hizmeti ağır yük altında. | [Arama hizmetinizin ölçeğini artırma](search-capacity-planning.md)
| Arama hizmeti 'nin hizmet güncelleştirmesi için düzeltme eki uygulanıyor veya bir topoloji yeniden yapılandırması ortasında. | Güncelleştirme diziniyle bağlantı kurulamadı. Arama hizmeti şu anda açık/arama hizmeti bir geçiş işlemi yaşıyor. | [SLA belgeleri](https://azure.microsoft.com/support/legal/sla/search/v1_0/) başına% 99,9 kullanılabilirlik için en az 3 çoğaltmalarla hizmeti yapılandırın
| Temeldeki işlem/ağ kaynağında hata (nadir) | Güncelleştirme diziniyle bağlantı kurulamadı. Bilinmeyen bir hata oluştu. | Başarısız bir durumdan almak için [bir zamanlamaya göre çalıştırılacak](search-howto-schedule-indexers.md) Dizin oluşturucularını yapılandırın.
| Ağ sorunları nedeniyle bir zaman aşımı süresi içinde hedef dizine yapılan bir dizin oluşturma isteği onaylanmadı. | Arama diziniyle zamanında bağlantı kurulamadı. | Başarısız bir durumdan almak için [bir zamanlamaya göre çalıştırılacak](search-howto-schedule-indexers.md) Dizin oluşturucularını yapılandırın. Ayrıca, bu hata durumu devam ederse Dizin Oluşturucu [toplu iş boyutunu](/rest/api/searchservice/create-indexer#parameters) azaltmayı deneyin.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Hata: belgenin bazı verileri geçerli olmadığından belge dizini oluşturulamadı

Belge, Dizin Oluşturucu tarafından okundu ve işlendi, ancak dizin alanları yapılandırmasındaki ve Dizin Oluşturucu tarafından ayıklanan ve işlenen verilerin eşleşmemesi nedeniyle arama dizinine eklenemedi. Bunun nedeni aşağıdakiler olabilir:

| Nedeni | Ayrıntılar/örnek
| --- | ---
| Dizin Oluşturucu tarafından ayıklanan alanların veri türü, karşılık gelen hedef dizin alanının veri modeliyle uyumsuz. | ' 888 ' anahtarına sahip belgedeki '_Data_' veri alanı ' Edm. String ' ' türünde geçersiz bir değere sahip. Beklenen tür ' Collection (EDM. String) ' idi. |
| Dize değerinden herhangi bir JSON varlığı ayıklanamadı. | '_Data_' alanının ' Edm. String ' ' DEĞERI bir JSON nesnesi olarak ayrıştırılamadı. Hata: ' bir değer ayrıştırdıktan sonra beklenmeyen bir karakterle karşılaşıldı: ' '. Yol '_yol_', satır 1, konum 3162. ' |
| JSON varlıklarının bir koleksiyonu bir dize değerinden ayıklanamadı.  | '_Data_' alanının ' Edm. String ' ' DEĞERI bir JSON dizisi olarak ayrıştırılamadı. Hata: ' bir değer ayrıştırdıktan sonra beklenmeyen bir karakterle karşılaşıldı: ' '. Yol ' [0] ', satır 1, konum 27. ' |
| Kaynak belgede bilinmeyen bir tür bulundu. | Bilinmeyen '_bilinmiyor_' türünün dizini oluşturulamaz |
| Kaynak belgede Coğrafya noktaları için uyumsuz bir gösterim kullanıldı. | WKT noktası dize sabit değerleri desteklenmiyor. Lütfen bunun yerine GeoJson nokta sabit değerlerini kullanın |

Tüm bu durumlarda, Dizin şemasını doğru bir şekilde oluşturup uygun [Dizin Oluşturucu alan eşlemelerini](search-indexer-field-mappings.md)ayarlamış olduğunuzdan emin olmak için, Dizin oluşturucular Için [desteklenen veri türleri](/rest/api/searchservice/supported-data-types) ve [veri türü eşlemesi](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) ' ne bakın. Hata iletisi, uyuşmazlığın kaynağını izlemeye yardımcı olabilecek ayrıntıları içerir.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Hata: tablo bileşik bir birincil anahtara sahip olduğundan, tümleşik değişiklik izleme ilkesi kullanılamıyor

Bu SQL tabloları için geçerlidir ve genellikle anahtar bileşik anahtar olarak tanımlandığında veya tablo benzersiz bir kümelenmiş dizin tanımlamışsa (bir SQL dizininde olduğu gibi, bir Azure Search dizininde olduğu gibi) olur. Ana neden, anahtar özniteliğinin [benzersiz bir kümelenmiş dizin](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)durumunda bileşik birincil anahtar olacak şekilde değiştirilmektedir. Bu durumda, SQL Tablonuzun benzersiz bir kümelenmiş dizine sahip olmadığından veya anahtar alanını yinelenen değerler içermediği garanti edilen bir alanla eşleştirdiğinizden emin olun.

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Hata: Dizin Oluşturucu en fazla çalışma süresi içinde belge işlenemedi

Dizin Oluşturucu, izin verilen yürütme süresi içinde veri kaynağından tek bir belgeyi işlemeyi tamamlayamediğinde bu hata oluşur. Becerileri kullanıldığında [en fazla çalışma süresi](search-limits-quotas-capacity.md#indexer-limits) daha kısadır. Bu hata oluştuğunda, maxFailedItems 0 dışında bir değere ayarladıysanız Dizin Oluşturucu, dizin oluşturmanın ilerlemesinin devam edebilmesi için gelecekteki çalışmadaki belgeyi atlar. Herhangi bir belgeyi atlamak için kabul ediyorsanız veya bu hatayı sürekli olarak görüyorsanız, tek bir Dizin Oluşturucu yürütmesi içinde kısmi ilerleme yapılabilmesi için belgeleri daha küçük belgelere bozmayı düşünün.

Name = "a-Not-Proje-Document><</a>

## <a name="error-could-not-project-document"></a>Hata: Proje belgesi oluşturulamadı

Bu hata, Dizin Oluşturucu [verileri bir bilgi deposuna proje](knowledge-store-projection-overview.md) yapmaya çalıştığında ve bunu yapmaya çalışımızda bir hata oluştuğu zaman oluşur.  Bu hata tutarlı ve düzeltilebilir olabilir ya da yansıtma çıkış havuzunda, çözmeniz için beklemeniz ve yeniden denemeniz gerekebilecek geçici bir hata olabilir.  Bilinen hata durumları ve olası çözümler kümesi aşağıda verilmiştir.

| Nedeni | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Kapsayıcıda projeksiyon blobu güncelleştirilemedi `'blobUri'``'containerName'` |Belirtilen kapsayıcı yok. | Dizin Oluşturucu, belirtilen kapsayıcının önceden oluşturulup oluşturulmadıysa denetler ve gerekirse onu oluşturur, ancak bu denetimi dizin oluşturucunun her yerine yalnızca bir kez çalıştırır. Bu hata, bu adımdan sonra kapsayıcının silindiği anlamına gelir.  Bu hatayı çözmek için şunu deneyin: depolama hesabı bilgilerinizi tek tek bırakın, dizin oluşturucunun bitmesini bekleyin ve ardından dizin oluşturucuyu yeniden çalıştırın. |
| Kapsayıcıda projeksiyon blobu güncelleştirilemedi `'blobUri'``'containerName'` |Aktarım bağlantısına veri yazılamıyor: mevcut bir bağlantı uzak ana bilgisayar tarafından zorla kapatıldı. | Bu, Azure depolama ile ilgili geçici bir hata olması beklenir ve bu nedenle dizin oluşturucunun yeniden çalıştırılarak çözülmesi gerekir. Bu hatayla sürekli karşılaşırsanız, daha fazla araştırılması için lütfen bir [destek bileti](https://ms.portal.azure.com/#create/Microsoft.Support) dosyası sağlayın.  |
| Tablodaki satır güncelleştirilemedi `'projectionRow'``'tableName'` | Sunucu meşgul. | Bu, Azure depolama ile ilgili geçici bir hata olması beklenir ve bu nedenle dizin oluşturucunun yeniden çalıştırılarak çözülmesi gerekir. Bu hatayla sürekli karşılaşırsanız, daha fazla araştırılması için lütfen bir [destek bileti](https://ms.portal.azure.com/#create/Microsoft.Support) dosyası sağlayın.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>Uyarı: yetenek girişi geçersizdi
Yeteneğe bir giriş eksikti, yanlış tür ya da başka bir şekilde geçersiz. Uyarı iletisi şu etkiyi gösterir:
1) Yetenek yürütülemedi
2) Yetenek yürütüldü ancak beklenmeyen sonuçlara neden olabilir

Bilişsel yetenekler için gerekli girişler ve isteğe bağlı girişler vardır. Örneğin, [anahtar tümceciği ayıklama becerinin](cognitive-search-skill-keyphrases.md) iki zorunlu girişi vardır `text` `languageCode` ve isteğe bağlı giriş yoktur. Özel beceri girişlerinin tümü isteğe bağlı girişler olarak kabul edilir.

Gerekli girişler eksikse veya herhangi bir giriş doğru türde değilse, yetenek atlanır ve bir uyarı oluşturur. Atlanan yetenekler hiçbir çıkış oluşturmaz, bu nedenle diğer yetenekler atlanan yeteneğin çıkışlarını kullanıyorsa, ek uyarılar oluşturabilir.

İsteğe bağlı bir giriş eksikse, yetenek hala çalışmaya devam eder, ancak eksik giriş nedeniyle beklenmedik bir çıkış oluşturabilir.

Her iki durumda da, verilerinizin şekli nedeniyle bu uyarı beklenmeyebilir. Örneğin, ve alanlarına sahip kişiler hakkında bilgi içeren bir belgeniz varsa, `firstName` `middleName` ve `lastName` için girişi olmayan bazı belgeleriniz olabilir `middleName` . İşlem hattındaki `middleName` bir yeteneğe girdi olarak geçmeniz durumunda, bu yetenek girişinde bazı bazı durumlarda eksik olabilir. Bu uyarının sonucu olarak herhangi bir eylemin gerekli olup olmadığını anlamak için verilerinizi ve senaryonuzu değerlendirmeniz gerekir.

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

| Nedeni | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Beceri girişi yanlış türde | "Gerekli yetenek girişi beklenen türde değildi `String` . Ad: `text` , kaynak: `/document/merged_content` . "  "Gerekli beceri girişi beklenen biçimde değil. Ad: `text` , kaynak: `/document/merged_content` . "  "Dizi olmayan bir üzerinde yinelenemez `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ."  "Dizi olmayan bir seçim yapılamıyor `0` `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` " | Belirli yetenekler belirli türlerin giriş beklentilerini bekler, örneğin yaklaşım [yeteneği](cognitive-search-skill-sentiment.md) `text` bir dize olmasını bekler. Giriş dize olmayan bir değer belirtiyorsa, yetenek yürütülmez ve çıkış oluşturmaz. Veri ayarlamış olduğunuz giriş değerlerinin türünde Tekdüzen olduğundan emin olun veya girişi önceden işlemek için [özel bir Web API 'si](cognitive-search-custom-skill-web-api.md) kullanın. Yeteneği bir dizi üzerinden yineleyorsanız, yetenek bağlamını ve girişin doğru konumlarda olduğunu kontrol edin `*` . Genellikle bağlam ve giriş kaynağının diziler için ile bitmesi gerekir `*` . |
| Yetenek girişi eksik | "Gerekli yetenek girişi eksik. Ad: `text` , kaynak: `/document/merged_content` "" eksik değer `/document/normalized_images/0/imageTags` . "  " `0` Uzunluk dizisinde seçim yapılamıyor `/document/pages` `0` ." | Tüm belgeleriniz bu uyarıyı alıyorsa, büyük olasılıkla giriş yollarında bir yazım hatası vardır ve bu durumda özellik adı büyük/küçük harf ' i çift kontrol etmeniz `*` ve veri kaynağındaki belgelerin gerekli girişleri sağlaması gerekir. |
| Yetenek dil kodu girişi geçersiz | Beceri girişi `languageCode` `X,Y,Z` , en az biri geçersiz olan aşağıdaki dil kodlarına sahiptir. | Daha fazla ayrıntı için [aşağıya](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) bakın |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Uyarı: ' languageCode ' yetenek girişi, en az bir tane geçersiz olan ' X, Y, Z ' dil kodlarına sahip.
Aşağı akış beceriye ait isteğe bağlı girişe geçirilen bir veya daha fazla değer `languageCode` desteklenmiyor. Bu durum, [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) çıktısını sonraki becerilerle geçirmeniz durumunda meydana gelir ve çıktı bu aşağı akış becerilerinizi desteklenenden daha fazla dilde oluşur.

Ayrıca `countryHint` , LanguageDetectionSkill 'e geçersiz bir giriş geçiriliyorsa buna benzer bir uyarı alabilirsiniz. Bu durumda, bu giriş için veri kaynağınızdan kullandığınız alanın geçerli ISO 3166-1 Alpha-2 2 Letter ülke kodları içerdiğini doğrulayın. Bazıları geçerliyse ve bazıları geçersizse, aşağıdaki kılavuzlarla devam edin, ancak ile ile değiştirin `languageCode` `countryHint` ve `defaultLanguageCode` `defaultCountryHint` kullanım örneği ile eşleştirin.

Veri ayarlamış olduğunuz tek bir dilde olduğunu biliyorsanız, [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) ve yetenek girişini kaldırmalı ve söz konusu beceri için `languageCode` `defaultLanguageCode` dilin desteklendiği varsayıldığında bu beceri için yetenek parametresini kullanmanız gerekir.

Veri ayarlamış olduğunuz birden çok dil içerdiğini ve bu nedenle [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) ve Input 'a ihtiyacınız olduğunu biliyorsanız `languageCode` , metni aşağı akış yeteneklerine geçirmeden önce desteklenmeyen dillerle metni filtrelemek Için bir [conditionalbeceri](cognitive-search-skill-conditional.md) eklemeyi göz önünde bulundurun.  İşte bunun Entityrecognitionbeceri için nasıl görünebileceğini bir örnek:

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
* [Desteklenen metin analizi diller](../cognitive-services/text-analytics/language-support.md) ( [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [entityrecognitionbeceri](cognitive-search-skill-entity-recognition.md), [sentimentbecerisi](cognitive-search-skill-sentiment.md)ve [PIIDetectionSkill](cognitive-search-skill-pii-detection.md)için)
* [Translator tarafından desteklenen diller](../cognitive-services/translator/language-support.md) ( [metin Için translationbeceri](cognitive-search-skill-text-translation.md)için)
* [Metin bölünmüş beceri](cognitive-search-skill-textsplit.md) Desteklenen Diller: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>Uyarı: yetenek girişi kesildi
Bilişsel yetenekler, bir kerede çözümlenebilecek metin uzunluğuna yönelik sınırlara sahiptir. Bu yeteneklerin metin girişi bu sınırın üzerinde ise, sınırı karşılamak için metni kısaltacak ve sonra bu kesilen metinde zenginleştirme gerçekleştiririz. Bu, yeteneğin yürütüldüğü, ancak tüm verilerinizin üzerinde olmadığı anlamına gelir.

Aşağıdaki örnekte, `'text'` giriş alanı karakter sınırının üzerinde ise bu uyarıyı tetikleyebiliriz. Beceri girişi sınırlarını [beceriler belgelerinde](cognitive-search-predefined-skills.md)bulabilirsiniz.

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

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Uyarı: Web API 'SI yetenek yanıtı uyarıları içeriyor
Dizin Oluşturucu beceri içinde bir yetenek çalıştırabiliyor, ancak Web API isteğinden alınan yanıt yürütme sırasında uyarılar olduğunu belirtti. Verilerinizin nasıl etkilendiğini ve eylem gerekip gerekmediğini anlamak için uyarıları gözden geçirin.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Uyarı: geçerli dizin oluşturucu yapılandırması artımlı ilerlemeyi desteklemiyor

Bu uyarı yalnızca Cosmos DB veri kaynakları için oluşur.

Dizin oluşturma sırasında artımlı ilerleme durumu, Dizin Oluşturucu yürütmesi geçici hatalara veya yürütme süresi sınırına göre kesintiye uğrarsa, dizin oluşturucunun, sıfırdan tüm koleksiyonu yeniden dizinlemesini sağlamak yerine, bir sonraki çalıştırıldığında kaldığınız yerden seçim yapmasına olanak sağlar. Büyük koleksiyonlar dizinlenirken bu özellikle önemlidir.

Tamamlanmamış bir dizin oluşturma işinin sürdürülebilmesi, sütun tarafından sıralanan belgelerin elde edileceği şekilde tahmin edilir `_ts` . Dizin Oluşturucu, sonraki hangi belgenin çalıştırılacağını belirleyen zaman damgasını kullanır. `_ts`Sütun eksikse veya Dizin Oluşturucu özel bir sorgunun bu tarafından sıralı olup olmadığını belirleyeemdi, Dizin Oluşturucu başlangıcında başlar ve bu uyarıyı görürsünüz.

Bu davranışı geçersiz kılmak, artımlı ilerleme durumunu etkinleştirmek ve yapılandırma özelliği kullanılarak bu uyarının gizlenmesi olabilir `assumeOrderByHighWatermarkColumn` .

Daha fazla bilgi için bkz. [artımlı ilerleme ve özel sorgular](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Uyarı: projeksiyon sırasında bazı veriler kayboldu. ' Y ' tablosundaki ' X ' satırı, çok uzun olan ' Z ' dize özelliğine sahip.

[Tablo depolama hizmeti](https://azure.microsoft.com/services/storage/tables) , büyük [varlık özelliklerinin](/rest/api/storageservices/understanding-the-table-service-data-model#property-types) nasıl kullanılabileceğini sınırlandırır. Dizelerde 32.000 karakter veya daha az olabilir. Dize özelliği 32.000 karakterden daha uzun bir satır yansıtılmakta ise, yalnızca ilk 32.000 karakter korunur. Bu sorunu geçici olarak çözmek için 32.000 karakterden uzun dize özellikleriyle satırları yansıtımaktan kaçının.

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Uyarı: ayıklanan metin X karakter olarak kesildi
Dizin oluşturucular, herhangi bir belgeden ne kadar metin ayıklanabileceği sayısını sınırlar. Bu sınır, fiyatlandırma katmanına bağlıdır: ücretsiz katman için 32.000 karakter, temel için 64.000, standart için 4.000.000, Standart S2 için 8.000.000 ve Standart S3 için 16.000.000. Kesilen metnin dizini oluşturulmaz. Bu uyarıyı önlemek için, büyük miktarlarda metinle birlikte belgeleri birden çok, daha küçük belgeye bölmek için kullanmayı deneyin. 

Daha fazla bilgi için bkz. [Dizin Oluşturucu sınırları](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Uyarı: ' X ' Çıkış alanı arama dizinine eşlenemiyor
Varolmayan/null olmayan verilere başvuran çıkış alanı eşlemeleri, her belge için uyarı üretir ve boş bir dizin alanına neden olur. Bu soruna geçici bir çözüm olarak, olası yazım hataları için çıkış alan eşleme kaynak yollarını iki kez kontrol edin veya [koşullu beceriye](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)kullanarak varsayılan bir değer ayarlayın. Ayrıntılar için bkz. [çıktı alan eşleme](cognitive-search-output-field-mapping.md) .

| Nedeni | Ayrıntılar/örnek | Çözüm |
| --- | --- | --- |
| Dizi olmayan üzerinde yinelenemez | "Dizi olmayan bir üzerinde yinelenemez `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ." | Bu hata, çıkış bir dizi olmadığında oluşur. Çıktının bir dizi olması gerektiğini düşünüyorsanız, belirtilen çıkış kaynağı alan yolunu hatalara karşı denetleyin. Örneğin, kaynak alanı adında eksik veya ekstra olabilir `*` . Bu beceri girişi null ve boş bir dizi ile sonuçlanır. [Yetenek girişinde](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) benzer ayrıntıları bul bölümü geçersiz.    |
| `0`Dizi olmayan içinde seçim yapılamıyor | "Dizi olmayan bir seçim yapılamıyor `0` `/document/pages` ." | Bu durum, yetenekler çıktısı bir dizi oluşturmuyorsa ve çıkış kaynağı alan adının dizi dizini veya yolunda olması durumunda meydana gelebilir `*` . Lütfen belirtilen alan adı için çıkış kaynağı alan adlarında ve alan değerinde belirtilen yolları çift kontrol edin. [Yetenek girişinde](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) benzer ayrıntıları bul bölümü geçersiz.  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Uyarı: veri değişikliği algılama ilkesi ' X ' anahtar sütununu kullanacak şekilde yapılandırıldı
[Veri değişikliği algılama ilkeleri](/rest/api/searchservice/create-data-source#data-change-detection-policies) , değişikliği algılamak için kullandıkları sütunlar için özel gereksinimlere sahiptir. Bu gereksinimlerden biri, kaynak öğe her değiştirildiğinde bu sütunun Güncellenme öğesidir. Başka bir gereksinim, bu sütun için yeni değerin önceki değerden daha büyük olması. Anahtar sütunlar, her güncelleştirmede değişmediği için bu gereksinimi yerine getirmiyor. Bu sorunu geçici olarak çözmek için değişiklik algılama ilkesi için farklı bir sütun seçin.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Uyarı: belge metni UTF-16 kodlamalı görünüyor, ancak bir bayt sıra işareti eksik

[Dizin Oluşturucu ayrıştırma modları](/rest/api/searchservice/create-indexer#blob-configuration-parameters) , metnin ayrıştırmadan önce nasıl kodlandığını bilmelidir. Metin kodlamasının en yaygın iki yolu UTF-16 ve UTF-8 ' dir. UTF-8, her karakterin 1 bayt ve 4 bayt uzunluğunda olduğu değişken uzunlukta bir kodlamadır. UTF-16, her karakterin 2 bayt uzunluğunda olduğu sabit uzunluklu bir kodlamadır. UTF-16, "big endian" ve "little endian" iki farklı çeşitlere sahiptir. Metin kodlaması, metinden önceki bir bayt dizisi olan "bayt sırası işareti" tarafından belirlenir.

| Encoding | Bayt sıra Işareti |
| --- | --- |
| UTF-16 Big endian | 0xFE 0xFF |
| UTF-16 little endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Bir bayt sıra işareti yoksa, metnin UTF-8 olarak kodlandığını kabul edilir.

Bu uyarıyı geçici olarak çözmek için, bu Blobun metin kodlamasının ne olduğunu saptayın ve uygun bayt sırası işaretini ekleyin.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Uyarı: ' X ' Cosmos DB koleksiyonunun bir yavaş dizin oluşturma ilkesi vardır. Bazı veriler kaybolmuş olabilir

[Yavaş](../cosmos-db/index-policy.md#indexing-mode) dizin oluşturma ilkelerine sahip koleksiyonlar tutarlı bir şekilde sorgulanamaz, bu da dizin oluşturucunun verileri eksiktir. Bu uyarıyı geçici olarak çözmek için dizin oluşturma ilkenizi tutarlı olarak değiştirin.

## <a name="warning-the-document-contains-very-long-words-longer-than-64-characters-these-words-may-result-in-truncated-andor-unreliable-model-predictions"></a>Uyarı: belge çok uzun sözcükler içeriyor (64 karakterden uzun). Bu sözcükler kesilmiş ve/veya güvenilir olmayan model tahminlerinin oluşmasına neden olabilir.

Bu uyarı Metin Analizi hizmetinden geçirilir.  Bazı durumlarda, belgeniz uzun bir URL (büyük olasılıkla bir anahtar tümceciği veya itici yaklaşım, vb.) içerdiğinde bu uyarıyı yoksaymak güvenlidir.  Bir sözcük 64 karakterden uzun olduğunda, model tahminlerini etkileyebilecek 64 karaktere kısaltılacağını unutmayın.
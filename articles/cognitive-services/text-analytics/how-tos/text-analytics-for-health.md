---
title: Sistem durumu için Metin Analizi kullanma
titleSuffix: Azure Cognitive Services
description: Sağlık bilgilerini, sistem durumu için Metin Analizi ile yapılandırılmamış klinik metinden nasıl ayıklayacağınızı ve etiketleyeceğinizi öğrenin.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 80a943d235783852f57832363b5af8048f010575
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599444"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Nasıl yapılır: sistem durumu için Metin Analizi kullanma (Önizleme)

> [!IMPORTANT] 
> Sistem durumu Metin Analizi, "olduğu gıbı" ve "tüm hatalarıyla bırlıkte" sağlanmış bir önizleme özelliğidir. Bu nedenle, **sistem durumu için metin analizi (Önizleme) herhangi bir üretim kullanımı için uygulanmamalıdır veya dağıtılmamalıdır.** Sağlık durumu için Metin Analizi, bir tıbbi cihaz, klinik destek, tanılama aracı ya da tanılama, sağlama, azaltma, işleme veya önlemeye yönelik başka bir teknoloji veya Microsoft tarafından bu amaçla kullanılması amaçlanan bir lisans ya da hak sağlanmaz. Bu özellik, profesyonel tıp önerisi veya sağlık görüşlerine, tanılama, işleme ya da sağlık uzmanı 'nın klinik yargılarına veya bu şekilde kullanılmamalıdır. Müşteri, sistem durumu için Metin Analizi herhangi bir kullanım işleminden yalnızca sorumludur. Microsoft, sağlık veya yetenek ile bağlantılı olarak belirtilen herhangi bir malzeme için Metin Analizi, herhangi bir kişinin sistem durumunu veya tıbbi gereksinimlerini karşılamasını garanti etmez. 


Sistem durumu Metin Analizi, Doctor 'ın notları, disşarj özetleri, klinik belgeler ve elektronik sistem durumu kayıtları gibi yapılandırılmamış metinlerden ilgili tıbbi bilgileri çıkaran ve etiketleyen Metin Analizi API'si hizmetinin bir özelliğidir.  Bu hizmeti kullanmanın iki yolu vardır: 

* [Web tabanlı API (zaman uyumsuz)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Bir Docker kapsayıcısı (zaman uyumlu)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Özellikler

Sistem durumu için Metin Analizi, adlandırılmış varlık tanıma (NER), ilişki ayıklama, varlık olumsuzlama ve Ingilizce metin üzerinde varlık bağlantısı kurarak yapılandırılmamış klinik ve Biyotıp metninde Öngörüler elde etmeyi sağlar.

### <a name="named-entity-recognition"></a>[Adlandırılmış Varlık Tanıma](#tab/ner)

Adlandırılmış varlık tanıma, bir veya daha fazla anlamsal tür ile ilişkilendirilebilen (örn. tanılama, yollara adı, belirti/işaret veya yaş gibi), yapılandırılmamış metinde belirtilen kelimeleri ve tümceleri algılar.

> [!div class="mx-imgBorder"]
> ![Sağlık durumu](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[İlişki ayıklama](#tab/relation-extraction)

İlişki ayıklama metinde bahsedilen kavramlar arasındaki anlamlı bağlantıları tanımlar. Örneğin, bir koşul adı bir zaman veya bir kısaltma ile tam açıklama arasında ilişkilendirerek bir "koşul süresi" ilişkisi bulunur.  

> [!div class="mx-imgBorder"]
> ![Sağlık YENIDEN](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Varlık Bağlama](#tab/entity-linking)

Varlık bağlama, metinde bahsedilen adlandırılmış varlıkları Birleşik Tıbbi dil sistemi (UMLS) dahil olmak üzere önceden tanımlanmış bir kavram veritabanında bulunan kavramlara ilişkilendirerek benzersiz varlıkları ayırt ediyor. Tıbbi kavramlar, ek bir normalleştirme biçimi olarak tercih edilen adlandırma de atanır.

> [!div class="mx-imgBorder"]
> ![Sağlık EL](../media/ta-for-health/health-entity-linking.png)

Sistem durumu Metin Analizi Birleşik Tıbbi dil sistemi ([Umls](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) Metaeşanlamlılar bilgi kaynağında bulunan sağlık ve biotıbbi sözcük dağarcıklarını bağlamayı destekler.

### <a name="assertion-detection"></a>[Onaylama algılaması](#tab/assertion-detection) 

Tıbbi içeriğin anlamı negatif veya koşullu Onaylamalar gibi değiştiriciler tarafından büyük ölçüde etkilenir. Bu, yanlış temsil edildiğinde kritik etkilere sahip olabilir. Sistem durumu Metin Analizi, metinde bulunan varlıklar için üç onay algılama kategorisini destekler: 

* kesin olarak belirlenemeyeceğinden
* Oluştur
* ilişkilendirme

> [!div class="mx-imgBorder"]
> ![Sistem durumu NEG](../media/ta-for-health/assertions.png)

---

Desteklenen varlıkların tam listesi için sistem durumu için Metin Analizi tarafından döndürülen [varlık kategorilerine](../named-entity-types.md?tabs=health) bakın. Güvenilirlik puanları hakkında daha fazla bilgi için [metin analizi saydamlık notuna](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context)bakın. 

### <a name="supported-languages-and-regions"></a>Desteklenen diller ve bölgeler

Sistem durumu Metin Analizi yalnızca Ingilizce dil belgelerini destekler. 

Sistem durumu barındırılan Web API 'SI için Metin Analizi Şu anda yalnızca şu bölgelerde kullanılabilir: Batı ABD 2, Doğu ABD 2, Orta ABD, Kuzey Avrupa ve Batı Avrupa.

## <a name="request-access-to-the-public-preview"></a>Genel önizlemeye erişim isteyin

Sistem durumu genel önizlemesi için Metin Analizi erişim istemek için bilişsel [Hizmetler istek formunu](https://aka.ms/csgate) doldurun ve iletin. Sistem durumu kullanımı için Metin Analizi faturalandırılmaz. 

Form, siz, şirketiniz ve kapsayıcısını kullanacağınız kullanıcı senaryosu hakkında bilgi ister. Formu gönderdikten sonra, Azure bilişsel hizmetler ekibi bunu gözden geçirir ve bir karar vererek size e-posta ile gönderilir.

> [!IMPORTANT]
> * Formunda, bir Azure aboneliği KIMLIĞIYLE ilişkili bir e-posta adresi kullanmanız gerekir.
> * Kullandığınız Azure kaynağının, onaylanan Azure abonelik KIMLIĞIYLE oluşturulmuş olması gerekir. 
> * Microsoft 'tan uygulamanızın durumuyla ilgili güncelleştirmeler için e-postanızı (hem gelen kutusu hem de önemsiz klasörler) denetleyin.

## <a name="using-the-docker-container"></a>Docker kapsayıcısını kullanma 

Kendi ortamınızda sistem durumu kapsayıcısı için Metin Analizi çalıştırmak için, [kapsayıcıyı indirmek ve yüklemek üzere aşağıdaki yönergeleri](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)izleyin.

## <a name="using-the-client-library"></a>İstemci kitaplığını kullanma

Metin Analizi istemci kitaplığının en son sürümü, istemci nesnesi kullanarak sistem durumu için Metin Analizi çağırmanızı sağlar. Başvuru belgelerine başvurun ve GitHub 'daki örneklere bakın:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>REST API isteği gönderiliyor 

### <a name="preparation"></a>Hazırlık

Sistem durumu Metin Analizi, üzerinde çalışmaya daha az miktarda metin verdiğiniz zaman daha yüksek kaliteli bir sonuç üretir. Bu, daha büyük metin blokları üzerinde daha iyi sonuç veren anahtar tümceciği ayıklama gibi bazı diğer Metin Analizi özelliklerden daha tersidir. Bu işlemlerden en iyi sonuçları elde etmek için, girişleri uygun şekilde yeniden ayarlamayı göz önünde bulundurun.

Bu biçimde JSON belgelerinize sahip olmanız gerekir: KIMLIK, metin ve dil. 

Belge boyutunun belge başına 5.120 karakter altında olması gerekir. Bir koleksiyonda izin verilen en fazla belge sayısı için kavramlar bölümündeki [veri limitleri](../concepts/data-limits.md?tabs=version-3) makalesine bakın. Koleksiyon, istek gövdesinde gönderilir.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Barındırılan zaman uyumsuz Web API 'SI için API isteğini yapılandırma

Hem kapsayıcı hem de barındırılan Web API 'SI için bir POST isteği oluşturmanız gerekir. İstediğiniz bölgedeki barındırılan Web API 'sine hızlı bir şekilde bir POST isteği oluşturmak ve göndermek için [sistem durumu BARıNDıRıLAN API başvurusu için metin analizi](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) [Postman](text-analytics-how-to-call-api.md)'ı, bir kıvrımlı komutu veya **API test konsolunu** kullanabilirsiniz. 

> [!NOTE]
> Hem zaman uyumsuz `/analyze` hem de `/health` uç noktalar yalnızca şu bölgelerde kullanılabilir: Batı ABD 2, Doğu ABD 2, Orta ABD, Kuzey Avrupa ve Batı Avrupa.  Bu uç noktalara başarılı istekler yapmak için lütfen kaynağınızın bu bölgelerden birinde oluşturulduğundan emin olun.

Aşağıda, sistem sağlığı API isteğinin GÖNDERI gövdesinde Metin Analizi eklenmiş bir JSON dosyası örneği verilmiştir:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Barındırılan zaman uyumsuz Web API yanıtı 

Bu POST isteği, zaman uyumsuz işlem için bir iş göndermek üzere kullanıldığından, yanıt nesnesinde bir metin yok.  Ancak, iş ve çıkış durumunu denetlemek için bir GET isteği oluşturmak üzere yanıt üst bilgilerinde işlem konumu ANAHTARıNıN değeri gereklidir.  POST isteğinin yanıt üstbilgisindeki Operation-location ANAHTARıNıN değerine bir örnek aşağıda verilmiştir:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/health/jobs/<jobID>`

İş durumunu denetlemek için POST yanıtının Operation-location anahtar üstbilgisi değerindeki URL 'ye bir GET isteği yapın.  Aşağıdaki durumlar, bir işin durumunu yansıtmak için kullanılır: `NotStarted` , `running` ,,,, `succeeded` `failed` `rejected` `cancelling` , ve `cancelled` .  

`NotStarted` `running` Get ISTEĞIYLE aynı URL 'ye BIR Delete http çağrısını içeren veya durumu ile bir işi iptal edebilirsiniz.  [Sistem durumu BARıNDıRıLAN API başvurusu için metin analizi](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob), silme çağrısı hakkında daha fazla bilgi bulunabilir.

Bir GET isteğinin yanıtı örneği aşağıda verilmiştir.  Çıktı, `expirationDateTime` çıktının temizlenme zamanından sonra (işin oluşturulduğu zamandan itibaren 24 saat) geçtiğinde alınabilir.

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-03-11T05:43:37Z",
    "createdDateTime": "2021-03-11T05:42:32Z",
    "expirationDateTime": "2021-03-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.94
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-03-01"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Kapsayıcı için API isteğini yapılandırma

Dağıttığınız kapsayıcıya bir sorgu göndermek için [Postman](text-analytics-how-to-call-api.md) veya örnek kıvrımlı isteği kullanabilirsiniz ve `serverURL` değişkeni uygun değerle değiştirin.  Kapsayıcının URL 'sindeki API sürümünün barındırılan API 'den farklı olduğunu aklınızda yapın.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Aşağıdaki JSON, sistem durumu API 'SI isteğinin GÖNDERI gövdesinde Metin Analizi eklenmiş bir JSON dosyası örneğidir:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Kapsayıcı yanıt gövdesi

Aşağıdaki JSON, Kapsayıcılı zaman uyumlu çağrıdan sistem durumu API 'SI yanıt gövdesi için Metin Analizi bir örneğidir:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>Onaylama çıktısı

Sistem durumu için Metin Analizi, tıbbi kavramlara eklenen ve metin içinde kavramların bağlamını daha derin anlamak sağlayan bilgilendirici öznitelikler olan onaylama değiştiricileri. Bu değiştiriciler, her biri farklı bir boyut üzerinde odaklanan ve birbirini dışlayan değerler kümesi içeren üç kategoriye ayrılmıştır. Her bir varlığa kategori başına yalnızca bir değer atanır. Her kategori için en sık kullanılan değer varsayılan değerdir. Hizmetin çıkış yanıtı yalnızca varsayılan değerden farklı onaylama değiştiricileri içerir.

**Belirsizlik**  : kavramın varlığı (mevcut ve yok) ile ilgili bilgiler ve ilgili metnin kendi varlığı ile nasıl ilişkili olduğu hakkında bilgi sağlar (belirli ve mümkün olan).
*   **Pozitif** [varsayılan]: kavram var veya gerçekleşti.
* **Negatif**: kavram artık mevcut değil veya hiç gerçekleşmedi.
* **Positive_Possible**: kavram olasılığı vardır ancak bazı belirsizlik vardır.
* **Negative_Possible**: kavramın varlığı çok düşüktür ancak bazı belirsizlik vardır.
* **Neutral_Possible**: kavram, her iki tarafa de bir yandan bir arada olmadan olabilir veya bulunmayabilir.

**Conditionitesi** : bir kavram varlığının belirli koşullara bağlı olup olmadığı hakkında bilgi sağlar. 
*   **Hiçbiri** [varsayılan]: kavram, kuramsal değildir ve belirli koşullara bağlı değildir.
*   **Kuramsal**: kavram gelecekte geliştirme veya oluşma olabilir.
*   **Koşullu**: kavram var veya yalnızca belirli koşullar altında gerçekleşir.

**İlişkilendirme** : kavramın metin veya başka birinin konusuyla ilişkili olup olmadığını açıklar.
*   **Subject** [varsayılan]: kavram, genellikle hasta olan metnin konusuyla ilişkilendirilir.
*   **Someone_Else**: kavram, metnin konusu olmayan biriyle ilişkilendirilir.


Onaylama işlemi algılama, belirsizlik kategorisi için bir negatif değer olarak, iç içe varlıkları temsil eder, örneğin:

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty&quot;: &quot;negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id&quot;: &quot;C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id&quot;: &quot;0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>İlişki ayıklama çıkışı

Sistem durumu Metin Analizi, öznitelikler ve varlık arasındaki ilişkiler de dahil olmak üzere farklı kavramlar arasındaki ilişkileri tanır (örneğin, gövde yapısının yönü, yollara dozu) ve varlıklar arasında (örneğin, kısaltma algılama).

**GRUBUNUN**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

> [!NOTE]
> * KOŞULA başvuran ilişkiler, tanılama varlık türüne veya SYMPTOM_OR_SIGN varlık türüne başvurabilir.
> * YOLLARA 'e başvuran ilişkiler MEDICATION_NAME varlık türüne veya MEDICATION_CLASS varlık türüne başvurabilir.
> * ZAMANA başvuran ilişkiler, zaman varlık türüne veya TARIH varlık türüne başvurabilir.

İlişki ayıklama çıkışı, URI başvurularını ve ilişki türündeki varlıkların atanan rollerini içerir. Örnek:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>Ayrıca bkz.

* [Metin Analizine genel bakış](../overview.md)
* [Adlandırılmış varlık kategorileri](../named-entity-types.md)
* [Yenilikler](../whats-new.md)

---
title: Makbuz formu tanıyıcı
titleSuffix: Azure Cognitive Services
description: Tanıma API 'SI kullanımı ve limitleriyle birlikte makbuz analizine ilişkin kavramları öğrenin.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 8248b3ed21561340e963c848dee4430c48829ab1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285306"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Form tanıyıcı önceden oluşturulmuş makbuz modeli

Azure form tanıyıcı, önceden oluşturulmuş giriş modelini kullanarak satış makbuzlarından bilgi çözümleyebilir ve ayıklayabilir. Ingilizce olarak yazılmış alındılardan önemli bilgileri ayıklamak için derin öğrenme modelleriyle güçlü [optik karakter tanıma (OCR)](../computer-vision/overview-ocr.md) olanaklarımızı birleştirir.

## <a name="understanding-receipts"></a>Alındıları anlama

Birçok işletme ve kişi hala satış makbuzlarından el ile ayıklanan verileri kullanır. Bu alındılardan verilerin otomatik olarak ayıklanması karmaşık olabilir. Alındılar kaba olabilir, okunması zor olabilir, elle yazılmış parçalar içerebilir ve düşük kaliteli akıllı telefon görüntüleri içerebilir. Ayrıca, makbuz şablonları ve alanları Pazar, bölge ve satıcı tarafından büyük ölçüde farklılık gösterebilir. Bu veri ayıklama ve alan algılama sorunları, girişin benzersiz bir sorunu işlemesini getirir.  

Makbuz API 'SI, büyük miktarda alma işlemi senaryolarını etkinleştirmek için optik karakter tanıma (OCR) ve önceden oluşturulmuş modelimizi kullanır. Makbuz API 'SI ile bir modeli eğitme gereksinimi yoktur. Makbuz görüntüsünü analiz alma API 'sine gönderin ve veriler ayıklanır.

![örnek alındısı](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>Giriş hizmeti ne yapar? 

Önceden oluşturulmuş alış Irsaliyesi hizmeti, satış girişlerinin içeriğini &mdash; bir restoran, satıcı veya Market mağazasında sıkça alacağınız alış irsaliyesinin türü olarak ayıklar.

### <a name="fields-extracted"></a>Ayıklanan alanlar

|Ad| Tür | Description | Metin | Değer (standartlaştırılmış çıkış) |
|:-----|:----|:----|:----| :----|
| Pottype | string | Satış alındısı türü | 'Nün |  |
| MerchantName | string | Okundu bilgisini veren satıcı adı | Contoso |  |
| MerchantPhoneNumber | phoneNumber | Belirtilen ticari telefon numarası | 987-654-3210 | + 19876543210 |
| MerchantAddress | string | Belirtilen satıcı adresi | 123 Main St Redmond WA 98052 |  |
| TransactionDate | date | Makbuzun verildiği tarih | 06 Haziran 2019 | 2019-06-26  |
| Işlem zamanı | time | Girişin verildiği zaman | 4:49 PM | 16:49:00  |
| Toplam | sayı | Tam işlem alındısı toplamı | $14,34 | 14,34 |
| Ara toplam | sayı | Genellikle vergi uygulanmadan önce, girişin ara toplamı | $12,34 | 12.34 |
| Vergi | sayı | Makbuzdaki vergi, genellikle satış vergisi veya eşdeğer | 2,00 USD | 2,00 |
| İpucu | sayı | Alıcı tarafından dahil edilen İpucu | $1,00 | 1,00 |
| Öğeler | nesne dizisi | Ad, miktar, birim fiyat ve ayıklanan Toplam fiyattan oluşan satır öğeleri ayıklandı | |
| Name | string | Öğe adı | Surface Pro 6 | |
| Miktar | sayı | Her öğenin miktarı | 1 | |
| Fiyat | sayı | Her öğe biriminin ayrı fiyatı | $999,00 | 999,00 |
| Toplam Fiyat | sayı | Satır öğesinin toplam fiyatı | $999,00 | 999,00 |

### <a name="additional-features"></a>Ek özellikler

Makbuz API 'SI aşağıdaki bilgileri de döndürür:

* Alan güven düzeyi (her alan ilişkili bir güven değeri döndürür)
* OCR ham metni (tüm alındı için OCR ayıklanan metin çıktısı)
* Her bir değer, çizgi ve sözcük için sınırlayıcı kutu

## <a name="try-it-out"></a>Deneyin

Form tanıyıcı alındı hizmetini denemek için çevrimiçi örnek UI aracına gidin:

> [!div class="nextstepaction"]
> [Önceden oluşturulmuş modelleri deneyin](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Giriş gereksinimleri

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Desteklenen yerel ayarlar 

* **Önceden oluşturulmuş makbuz v 2.0** (GA), en-US yerel ayarında satış alındılarını destekler
* **Önceden oluşturulmuş makbuz v 2.1-Önizleme. 3** (Genel Önizleme) aşağıdaki en fazla ön ek ayarlar için ek destek ekler: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-ıN 

  > [!NOTE]
  > Dil girişi 
  >
  > Önceden oluşturulmuş makbuz v 2.1-Preview. 3, ek Ingilizce pazarlardan bir makbuz yerel ayarı belirtmek için isteğe bağlı bir istek parametresine sahiptir. Avustralya (EN-AU), Kanada (EN-CA), Büyük Britanya (EN-GB) ve Hindistan (EN-ın) ile Ingilizce olan satış alındıları için, iyileştirilmiş sonuçları elde etmek üzere yerel ayarı belirtebilirsiniz. V 2.1-Preview içinde yerel ayar belirtilmemişse, model varsayılan olarak EN-US modeline ayarlanır.


## <a name="the-analyze-receipt-operation"></a>Alındısı Analizi işlemi

[Analiz alma alındısı](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync) , giriş olarak bir görüntünün GÖRÜNTÜSÜNÜ veya PDF alır ve ilgilendiğiniz ve metnin değerlerini ayıklar. Çağrı, adlı bir yanıt üst bilgisi alanı döndürüyor `Operation-Location` . `Operation-Location`Değer, bir sonraki adımda kullanılacak sonuç kimliğini içeren BIR URL 'dir.

|Yanıt üst bilgisi| Sonuç URL 'SI |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>Analiz alma sonucunu al işlemi

İkinci adım, [analiz alma sonucunu Al işlemini çağıralım](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult) . Bu işlem, alındı bilgisi çözümleme işlemi tarafından oluşturulan sonuç KIMLIĞINI giriş olarak alır. Aşağıdaki olası değerlere sahip bir **durum** alanı IÇEREN bir JSON yanıtı döndürür. **Başarılı** değerle döndürülünceye kadar bu işlemi tekrarlayarak çağırın. Saniye başına istek (RPS) oranını aşmamak için 3 ile 5 saniye arasında bir Aralık kullanın.

|Alan| Tür | Olası değerler |
|:-----|:----:|:----|
|durum | string | notStarted: işlem başlatılmamış. |
| |  | çalışıyor: analiz işlemi devam ediyor. |
| |  | başarısız: çözümleme işlemi başarısız oldu. |
| |  | başarılı: çözümleme işlemi başarılı oldu. |

**Durum** alanı **başarılı** DEĞERINE sahip olduğunda, JSON yanıtı, makbuz anlama ve metin tanıma sonuçlarını içerir. Okundu bilgisi sonucu, adlandırılmış alan değerlerinin bir sözlüğü olarak düzenlenir. Her bir değer ayıklanan metin, normalleştirilmiş değer, sınırlayıcı kutusu, güvenirlik ve karşılık gelen Word öğelerini içerir. Metin tanıma sonucu, metin, sınırlayıcı kutusu ve güvenle bilgi içeren bir satır ve sözcük hiyerarşisi olarak düzenlenir.

![örnek alma sonuçları](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Örnek JSON çıkışı


Kaynağı çözümleme sonucunu al işleminin yanıtı, ayıklanan tüm bilgiler ile girişin yapılandırılmış temsili olacaktır.  [Örnek bir makbuz dosyası](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg) ve yapılandırılmış çıkış [örneği alındı çıktısı](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)için buraya bakın.

Başarılı bir JSON yanıtı örneğine bakın:
* `"readResults"`Düğüm, tüm tanınan metni içerir. Metin sayfaya, sonra satıra, sonra da tek sözcüklere göre düzenlenir. 
* `"documentResults"`Düğüm, modelin bulduğu iş kartına özgü değerleri içerir. Burada ad, soyadı, şirket adı ve daha fazlası gibi faydalı anahtar/değer çiftleri bulacaksınız.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="customer-scenarios"></a>Müşteri senaryoları  

Makbuz API 'SI ile ayıklanan veriler çeşitli görevleri gerçekleştirmek için kullanılabilir. Aşağıda, müşterilerin alındı API 'SI ile neler yaptığı hakkında birkaç örnek verilmiştir.

### <a name="business-expense-reporting"></a>İş gider raporlaması  

Genellikle iş giderlerinin dosyalanması, girişlerin görüntülerinden verileri el ile girerek harcama süresini içerir. Alındı API 'SI ile bu işlemi kısmen otomatikleştirebilmek ve alındılarınızı hızlıca analiz etmek için ayıklanan alanları kullanabilirsiniz.  

Giriş API 'SI, ayıklanan alan değerlerini birden çok şekilde kullanmanıza olanak tanıyan basit bir JSON çıktıdır. Gider raporlarını önceden doldurmak için dahili gider uygulamalarıyla tümleştirin. Bu senaryo hakkında daha fazla bilgi edinmek için Acumatika 'ın alındı API 'sini kullanarak, daha [az bir işleme raporlama yapmasını](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure)öğrenin.  

### <a name="auditing-and-accounting"></a>Denetim ve hesaplama

Makbuz API 'SI çıkışı Ayrıca, gider raporlama ve geri ödeme sürecinde çeşitli noktalarda çok sayıda gider analizi gerçekleştirmek için de kullanılabilir. El ile denetim veya hızlı onaylar için bunları önceliklendirme için alındıları işleyebilirsiniz.  

Makbuz çıktısı, iş veya kişisel kullanım için genel muhasebe tutulması için de kullanışlıdır. Herhangi bir ham Alım görüntüsünü/PDF verilerini eyleme dönüştürülebilir bir dijital çıkışa dönüştürmek için alındı API 'sini kullanın.

### <a name="consumer-behavior"></a>Tüketici davranışı 

Alındılar, tüketici davranışını ve alışveriş eğilimlerini çözümlemek için kullanabileceğiniz faydalı verileri içerir.

Makbuz API 'SI Ayrıca [AI Oluşturucu alma işlemi özelliğini](/ai-builder/prebuilt-receipt-processing)de güçlendirir.

## <a name="next-steps"></a>Sonraki adımlar

 . Seçtiğiniz geliştirme dilinde form tanıyıcı ile bir makbuz işleme uygulaması yazmaya başlayın.

> [!div class="nextstepaction"]
> [Form tanıyıcı hızlı başlangıcını tamamlar](quickstarts/client-library.md)

## <a name="see-also"></a>Ayrıca bkz.

* [Form Tanıma nedir?](overview.md)
* [Form tanıyıcı API başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)
>

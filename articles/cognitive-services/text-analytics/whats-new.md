---
title: Metin Analizi API'si yenilikler
titleSuffix: Text Analytics - Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler Metin Analizi yeni sürümler ve özellikler hakkında bilgi verilmektedir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: f1e509156beea0b3da3539306dc055291cbff0c8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314193"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Metin Analizi API’sindeki yenilikler

Metin Analizi API'si, sürekli olarak güncelleştirilir. Son geliştirmelerin güncel kalması için, bu makalede yeni yayınlar ve özellikler hakkında bilgi verilmektedir.

## <a name="march-2021"></a>Mart 2021

### <a name="general-api-updates"></a>Genel API güncelleştirmeleri
* Yeni API v 3.1-Önizleme. 4 öğesini içeren sürümü 
   * Görüşleriniz araştırma JSON yanıtı gövdesinde değişiklikler: 
      * `aspects` Şimdi `targets` `opinions` `assessments` . 
   * Barındırılan Web API 'sinin JSON yanıt gövdesinde sistem durumu için Metin Analizi değişiklikler: 
      * `isNegated`Algılanan bir varlık nesnesinin olumsuzlama için Boole adı kullanım dışıdır ve onaylama Işlemi algılama ile değiştirilmiştir.
      * Adlı yeni bir özellik `role` artık bir öznitelik ile bir varlık ve varlıklar arasındaki ilişki arasındaki ayıklanan ilişkinin bir parçasıdır.  Bu, algılanan ilişki türüne ayrıntısıyla ekler.
   * Varlık bağlama artık uç noktada zaman uyumsuz bir görev olarak kullanılabilir `/analyze` .
   * `pii-categories`Artık uç noktada yeni bir parametre kullanılabilir `/pii` .
      * Bu parametre, select PII varlıklarının yanı sıra giriş dili için varsayılan olarak desteklenenlerden istediğinizi belirtmenize olanak tanır.
* Zaman uyumsuz analiz ve sistem durumu işlemleri için Metin Analizi içeren güncelleştirilmiş istemci kitaplıkları. GitHub 'da örnekleri bulabilirsiniz:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript)
    
> [!div class="nextstepaction"]
> [Metin Analizi API'si v 3.1-önizleme hakkında daha fazla bilgi edinin. 4](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/operations/Languages)

### <a name="text-analytics-for-health-updates"></a>Sistem durumu güncelleştirmeleri için Metin Analizi

* `2021-03-01` `/health` Uç nokta ve şirket içi kapsayıcı için yeni bir model sürümü sağlar
    * Varlık türünün yeniden adlandırılması `Gene` `GeneOrProtein` .
    * Yeni bir `Date` varlık türü.
    * Değilleme algılamayı değiştiren onaylama işlemi algılama (yalnızca API v 3.1-Preview. 4).
    * `name`Çeşitli ontolobir ve kodlama sistemlerinden normalleştirilmiş (yalnızca API v 3.1-Preview. 4 ' te mevcuttur) bağlantılı varlıklar için yeni bir tercih edilen özellik. 
* Kapsayıcı önizleme deposuna etiketli yeni bir kapsayıcı görüntüsü `3.0.015490002-onprem-amd64` ve yeni model sürümü `2021-03-01` yayımlandı. 
    * Bu kapsayıcı görüntüsü artık `containerpreview.azurecr.io` 26 nisan 2021 tarihinden sonra yüklenmek üzere kullanılamayacak.
* Aynı model sürümüne sahip bir sistem durumu kapsayıcısı görüntüsü için yeni bir Metin Analizi Şu anda kullanılabilir `mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare` . 26 Nisan 'dan başlayarak kapsayıcıyı yalnızca bu depodan indirebilirsiniz.

> [!div class="nextstepaction"]
> [Sistem durumu Metin Analizi hakkında daha fazla bilgi edinin](how-tos/text-analytics-for-health.md)

### <a name="text-analytics-resource-portal-update"></a>Metin Analizi kaynak portalı güncelleştirmesi
* **Işlenen metin kayıtları** artık Azure Portal metin analizi kaynağınız için **izleme** bölümünde bir ölçüm olarak sunulmaktadır.  

## <a name="february-2021"></a>Şubat 2021

* `2021-01-15` [Adlandırılmış varlık tanıma](how-tos/text-analytics-how-to-entity-linking.md) v 3.1-Preview. x içindeki PII uç noktasının model sürümü 
  * 9 yeni dil için genişletilmiş destek
  * Desteklenen diller için adlandırılmış varlık kategorilerinin AI kalitesi geliştirildi.
* S0 ile S4 fiyatlandırma katmanları, 8 Mart 2021 ' de kullanımdan kaldırılıyor. S0 ile S4 fiyatlandırma katmanını kullanarak mevcut bir Metin Analizi kaynağınız varsa, standart [fiyatlandırma katmanını](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)kullanmak için onu güncelleştirmeniz gerekir.
* [Dil algılama kapsayıcısı](how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment) artık genel kullanıma sunulmuştur.
* API 'nin v 2.1 kullanımdan kaldırılıyor. 

## <a name="january-2021"></a>Ocak 2021

* `2021-01-15`Sağlayan, [adlandırılmış varlık tanıma](how-tos/text-analytics-how-to-entity-linking.md) v3. x için model sürümü 
  * [Birkaç genel varlık kategorisi](named-entity-types.md)için genişletilmiş dil desteği. 
  * Desteklenen tüm v3 dilleri için genel varlık kategorilerinin Geliştirilmiş AI kalitesi. 

* `2021-01-05` [Dil algılama](how-tos/text-analytics-how-to-language-detection.md)için model sürümü, ek [dil desteği](language-support.md?tabs=language-detection)sağlar.

Bu model sürümleri, Doğu ABD bölgesinde şu anda kullanılamıyor. 

> [!div class="nextstepaction"]
> [Yeni NER modeli hakkında daha fazla bilgi edinin](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>Aralık 2020

* Metin Analizi API'si için [fiyatlandırma ayrıntıları güncelleştirildi](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) .

## <a name="november-2020"></a>Kasım 2020

* NER, PII ve anahtar tümceciği ayıklama işlemleri için toplu işlemeyi destekleyen yeni zaman uyumsuz [analiz API 'si](how-tos/text-analytics-how-to-call-api.md?tabs=analyze)için Metin Analizi API'si v 3.1-Preview. 3 içeren [Yeni bir uç nokta](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) .
* Toplu işleme desteğiyle [sistem durumu](how-tos/text-analytics-for-health.md) barındırılan API için yeni zaman uyumsuz Metin Analizi için Metin Analizi API'si v 3.1-Preview. 3 ile [Yeni bir uç nokta](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) .
* Yukarıda listelenen yeni özelliklerin her ikisi de yalnızca şu bölgelerde kullanılabilir: `West US 2` , `East US 2` , `Central US` , `North Europe` ve `West Europe` bölgeleri.
* Portekizce (Brezilya) `pt-BR` , model sürümü ile başlayarak [yaklaşım Analizi](how-tos/text-analytics-how-to-sentiment-analysis.md) v3. x sürümünde desteklenmektedir `2020-04-01` . `pt-PT`Portekizce için mevcut desteğe ekler.
* Zaman uyumsuz analiz ve sistem durumu işlemleri için Metin Analizi içeren güncelleştirilmiş istemci kitaplıkları. GitHub 'da örnekleri bulabilirsiniz:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * 
> [!div class="nextstepaction"]
> [Metin Analizi API'si v 3.1-önizleme hakkında daha fazla bilgi edinin. 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>Ekim 2020

* Model sürümünden itibaren Yaklaşım Analizi v3. x için Hintçe desteği `2020-04-01` . 
* `2020-09-01`Daha fazla dil algılama ve doğruluk iyileştirmeleri ekleyen v3/diller uç noktası için model sürümü.
* Orta Hindistan ve BAE Kuzey v3 kullanılabilirliği.

## <a name="september-2020"></a>Eylül 2020

### <a name="general-api-updates"></a>Genel API güncelleştirmeleri

* Aşağıdaki adlandırılmış varlık tanıma v3 uç noktalarında güncelleştirmeleri desteklemek üzere Metin Analizi v 3.1 Genel önizlemesi için yeni bir URL yayını: 
    * `/pii` uç nokta artık, `redactedText` Yanıt JSON öğesinde, giriş metninde ALGıLANAN PII varlıklarının bu varlıkların her bir karakteriyle değiştirildiği yeni özelliği içerir `*` .
    * `/linking` uç nokta artık `bingID` bağlantılı varlıklar için Response JSON içindeki özelliği içerir.
* Aşağıdaki Metin Analizi Preview API uç noktaları 4 Eylül 2020 ' de kullanımdan kaldırıldı:
    * v 2.1-Önizleme
    * v3.0-preview
    * v 3.0-Önizleme. 1
    
> [!div class="nextstepaction"]
> [Metin Analizi API'si v 3.1-önizleme hakkında daha fazla bilgi edinin. 2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>Sistem durumu kapsayıcısı güncelleştirmeleri için Metin Analizi

Aşağıdaki güncelleştirmeler, yalnızca sistem durumu kapsayıcısı için Metin Analizi Eylül sürümüne özgüdür.
* `1.1.013530001-amd64-preview`Kapsayıcı önizleme deposuna yeni model sürümü olan etiketli yeni bir kapsayıcı görüntüsü `2020-09-03` yayımlandı. 
* Bu model sürümü, varlık tanıma, kısaltma algılama ve gecikme süresi geliştirmeleriyle ilgili iyileştirmeler sağlar.

> [!div class="nextstepaction"]
> [Sistem durumu Metin Analizi hakkında daha fazla bilgi edinin](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>Ağustos 2020

### <a name="general-api-updates"></a>Genel API güncelleştirmeleri

* `2020-07-01`V3 için model sürümü `/keyphrases` ve şunları `/pii` `/languages` ekleyen uç noktalar:
    * Adlandırılmış varlık tanıma için ek kamu ve ülkeye özgü [varlık kategorileri](named-entity-types.md?tabs=personal) .
    * Yaklaşım Analizi v3 'de Norveç ve Türkçe desteği.
* Artık yayımlanan [veri sınırlarını](concepts/data-limits.md)aşan v3 API istekleri IÇIN bir HTTP 400 hatası döndürülür. 
* Bir sapmayı döndüren uç noktalar artık `stringIndexType` , döndürülen `offset` ve `length` değerleri desteklenen bir [dize Dizin düzeniyle](concepts/text-offsets.md)eşleşecek şekilde ayarlayan isteğe bağlı parametreyi destekler.

### <a name="text-analytics-for-health-container-updates"></a>Sistem durumu kapsayıcısı güncelleştirmeleri için Metin Analizi

Aşağıdaki güncelleştirmeler, yalnızca sistem durumu kapsayıcısı için Metin Analizi Ağustos sürümüne özgüdür.

* Sistem durumu için yeni model sürümü Metin Analizi: `2020-07-24`
* Sistem durumu istekleri için Metin Analizi göndermek üzere yeni URL: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (Bu yeni kapsayıcı görüntüsünde bulunan demo Web uygulamasını kullanmak için bir tarayıcı önbelleği temizleme 'nin gerekli olacağını lütfen unutmayın)

JSON yanıtında aşağıdaki özellikler değiştirildi:

* `type`, `category` olarak yeniden adlandırıldı 
* `score`, `confidenceScore` olarak yeniden adlandırıldı
* `category`JSON çıktısının alanındaki varlıklar artık Pascal durumunda. Aşağıdaki varlıklar yeniden adlandırıldı:
    * `EXAMINATION_RELATION` , olarak yeniden adlandırıldı `RelationalOperator` .
    * `EXAMINATION_UNIT` , olarak yeniden adlandırıldı `MeasurementUnit` .
    * `EXAMINATION_VALUE` , olarak yeniden adlandırıldı `MeasurementValue` .
    * `ROUTE_OR_MODE` yeniden adlandırıldı `MedicationRoute` .
    * İlişkisel varlık `ROUTE_OR_MODE_OF_MEDICATION` olarak yeniden adlandırıldı `RouteOfMedication` .

Aşağıdaki varlıklar eklendi:

* HI
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* İlişki ayıklama
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Sistem durumu kapsayıcısı için Metin Analizi hakkında daha fazla bilgi edinin](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Temmuz 2020 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Sistem durumu kapsayıcısı için Metin Analizi-genel geçişli Önizleme

Sistem durumu kapsayıcısı için Metin Analizi, artık genel geçitli önizlemededir ve bu, örneğin: hasta Intake formları, Doktor notları, araştırma kağıtları ve ücretsiz Özet özetleri gibi klinik belgelerde yapılandırılmamış Ingilizce dilindeki metinden bilgi ayıklamanıza olanak tanır. Şu anda, sistem durumu kapsayıcısı kullanımı için Metin Analizi faturalandırılmaz.

Kapsayıcı aşağıdaki özellikleri sunar:

* Adlandırılmış Varlık Tanıma
* İlişki ayıklama
* Varlık bağlama
* Negatif

## <a name="may-2020"></a>Mayıs 2020

### <a name="text-analytics-api-v3-general-availability"></a>Metin Analizi API'si v3 genel kullanılabilirliği

Metin analizi API v3, artık aşağıdaki güncelleştirmelerle genel kullanıma sunulmuştur:

* Model sürümü `2020-04-01`
* Her özellik için yeni [veri limitleri](concepts/data-limits.md)
* [Yaklaşım Analizi (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) için [dil desteği](language-support.md) güncelleştirildi
* Varlık bağlama için ayrı uç nokta 
* [Adlandırılmış varlık tanıma (ner) v3](how-tos/text-analytics-how-to-entity-linking.md)içindeki yeni "adres" varlık kategorisi.
* NER v3 içindeki yeni alt kategoriler:
   * Konum-coğrafi
   * Konum-yapısal
   * Kuruluş-stok alışverişi
   * Kuruluş-tıp
   * Kuruluş-spor
   * Olay-kültürel
   * Olay-doğal
   * Olay-spor

JSON yanıtında aşağıdaki özellikler eklendi:
   * `SentenceText` Yaklaşım Analizi
   * `Warnings` Her belge için 

JSON yanıtında aşağıdaki özelliklerin adları, uygun yerlerde değiştirilmiştir:

* `score`, `confidenceScore` olarak yeniden adlandırıldı
    * `confidenceScore` iki ondalık duyarlığa sahiptir. 
* `type`, `category` olarak yeniden adlandırıldı
* `subtype`, `subcategory` olarak yeniden adlandırıldı

> [!div class="nextstepaction"]
> [Metin Analizi API'si v3 hakkında daha fazla bilgi edinin](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Metin Analizi API'si v 3.1 genel önizleme
   * Yeni Yaklaşım Analizi özelliği- [görüşme madenciliği](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * `PII`Korunan sağlık bilgileri () için yeni kişisel () etki alanı filtresi `PHI` .

> [!div class="nextstepaction"]
> [Metin Analizi API'si v 3.1 Preview hakkında daha fazla bilgi edinin](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Şubat 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Metin Analizi API'si v3 genel önizleme için SDK desteği

[Birleşik Azure SDK sürümünün](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)bir parçası olarak, metın ANALIZI API'SI v3 SDK artık aşağıdaki programlama dilleri için genel önizleme olarak sunulmaktadır:
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Metin Analizi API'si v3 SDK hakkında daha fazla bilgi edinin](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Adlandırılmış varlık tanıma v3 genel önizlemesi

Ek varlık türleri artık, metinde bulunan genel ve kişisel bilgi varlıklarının algılanmasını genişlettiğimiz için adlandırılmış varlık tanıma (NER) v3 genel önizleme hizmetinde kullanılabilir. Bu güncelleştirme aşağıdakileri içeren [model sürümünü](concepts/model-versioning.md) tanıtır `2020-02-01` :

* Aşağıdaki genel varlık türlerini tanıma (Yalnızca Ingilizce):
    * Kişilik türü
    * Ürün
    * Olay
    * Konum altında alt tür olarak geopolitik varlık (GPE)
    * İmde

* Aşağıdaki kişisel bilgi varlığı türlerini tanıma (Yalnızca Ingilizce):
    * Kişi
    * Kuruluş
    * Miktar altında alt tür olarak yaş
    * DateTime altındaki alt tür olarak Tarih
    * E-posta 
    * Telefon numarası (yalnızca ABD)
    * URL
    * IP Adresi

### <a name="october-2019"></a>Ekim 2019

#### <a name="named-entity-recognition-ner"></a>Adlandırılmış varlık tanıma (NER)

* Kişisel bilgi varlık türlerini (Yalnızca Ingilizce) tanıma için [Yeni bir uç nokta](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii)

* [Varlık tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) ve [varlık bağlama](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)için ayrı uç noktalar.

* [Model sürümü](concepts/model-versioning.md) şunları `2019-10-01` içerir:
    * Metinde bulunan varlıkların genişletilmiş algılanması ve kategorilere ayrılması. 
    * Aşağıdaki yeni varlık türlerinin tanınması:
        * Telefon numarası
        * IP Adresi

Varlık bağlama, Ingilizce ve Ispanyolca 'yi destekler. NER dil desteği varlık türüne göre farklılık gösterir.

#### <a name="sentiment-analysis-v3-public-preview"></a>Yaklaşım Analizi v3 genel önizleme

* Yaklaşımı çözümlemek için [Yeni bir uç nokta](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) .
* [Model sürümü](concepts/model-versioning.md) şunları `2019-10-01` içerir:

    * API 'nin metin kategorisi ve Puanlama açısından doğruluk ve ayrıntılarda önemli geliştirmeler.
    * Metindeki farklı sentiler için otomatik etiketleme.
    * Bir belge ve tümce düzeyinde yaklaşım Analizi ve çıktısı. 

İngilizce (), Japonca (), Çince Basitleştirilmiş (), Çince Geleneksel (), Fransızca (), İtalyanca (), İspanyolca (), Felemenkçe () `en` `ja` `zh-Hans` `zh-Hant` `fr` `it` `es` `nl` , Portekizce () ve `pt` Almanca ( `de` ) destekler ve şu bölgelerde kullanılabilir: `Australia East` , `Central Canada` , `Central US` , `East Asia` ,, `East US` `East US 2` , `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,,, ve. 

> [!div class="nextstepaction"]
> [Yaklaşım Analizi v3 hakkında daha fazla bilgi edinin](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Sonraki adımlar

* [Metin Analizi API'si nedir?](overview.md)  
* [Örnek kullanıcı senaryoları](text-analytics-user-scenarios.md)
* [Yaklaşım analizi](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Dil algılama](how-tos/text-analytics-how-to-language-detection.md)
* [Varlık tanıma](how-tos/text-analytics-how-to-entity-linking.md)
* [Anahtar tümceciği ayıklama](how-tos/text-analytics-how-to-keyword-extraction.md)

---
title: Metin Analizi API'si nedir? Yetenek
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler 'den, yaklaşım analizi, anahtar ifade ayıklama, dil algılama ve varlık tanıma için Metin Analizi API'si kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/07/2019
ms.author: aahi
ms.openlocfilehash: 0f16c687f9816e99e05c2495a05596bd988ca9d2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827313"
---
# <a name="what-is-the-text-analytics-api"></a>Metin Analizi API'si nedir?

Metin Analizi API'si, ham metin üzerinde gelişmiş doğal dil işleme sağlayan bulut tabanlı bir hizmettir ve dört ana işlev içerir: yaklaşım analizi, anahtar ifade ayıklama, dil algılama ve varlık tanıma.

API, geliştirme projeleriniz için bulutta makine öğrenimi ve AI algoritmaları koleksiyonu olan Azure bilişsel [Hizmetler](https://docs.microsoft.com/azure/cognitive-services/)'in bir parçasıdır.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Metin analizi farklı şeyler anlamına gelebilir, ancak bilişsel hizmetler 'de Metin Analizi API'si, aşağıda açıklandığı gibi dört tür analiz sağlar. Bu özellikleri [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)veya [istemci kitaplığıyla](quickstarts/text-analytics-sdk.md)birlikte kullanabilirsiniz.

## <a name="sentiment-analysis"></a>Duygu Analizi
Olumlu veya olumsuz yaklaşım hakkında ipuçları için ham metni analiz ederek hangi müşterilerin markanızı veya konuyu düşündüğünü öğrenmek için yaklaşım [analizini](how-tos/text-analytics-how-to-sentiment-analysis.md) kullanın. API, her belge için 0 ile 1 arasında bir yaklaşım puanı döndürür ve 1 en pozitif değerdir.<br /> Analiz modelleri, Microsoft tarafından sağlanan geniş kapsamlı gövde metinleri ve doğal dil teknolojileri kullanılarak önceden eğitilmiştir. API, [seçili dillerde](text-analytics-supported-languages.md) sağladığınız ham metni analiz edip puanlayabilir ve sonuçları doğrudan çağrıyı yapan uygulamaya döndürebilir.

## <a name="key-phrase-extraction"></a>Anahtar İfade Ayıklama
Ana noktaları hızlı bir şekilde tanımlamak için [anahtar tümceleri](how-tos/text-analytics-how-to-keyword-extraction.md) otomatik olarak ayıklayın. Örneğin, "The food was delicious and there were wonderful staff" (Yemekler lezzetliydi ve personel harikaydı) giriş metni olduğunda API, "food" (yemek) ve "wonderful staff" (personel harikaydı) ana konuşma noktalarını döndürür.

## <a name="language-detection"></a>Dil Algılama
[Giriş metninin hangi dilde yazıldığını algılayabilir](how-tos/text-analytics-how-to-language-detection.md) ve çok çeşitli diller, çeşitler, diapacts ve bazı bölgesel/kültürel dillerinde istek üzerine gönderilen her belge için tek bir dil kodu rapor edebilirsiniz. Dil kodu, puanın ağırlığını belirten bir puanla eşleştirilir.

## <a name="named-entity-recognition"></a>Adlandırılmış Varlık Tanıma
Metninizdeki varlıkları kişiler, konumlar, kuruluşlar, tarih/saat, miktarlar, yüzdeler, para birimleri ve daha fazlası olarak [belirleyip kategorilere ayırın](how-tos/text-analytics-how-to-entity-linking.md) . İyi bilinen varlıklar da tanınarak web üzerindeki ek bilgilere bağlantı verilir.

## <a name="use-containers"></a>Kapsayıcıları kullanma

Verilerinize daha yakın standartlaştırılmış Docker Kapsayıcıları yükleyerek anahtar tümceleri ayıklamak, dili algılamak ve yaklaşımı yerel olarak çözümlemek için [metin analizi kapsayıcıları kullanın](how-tos/text-analytics-how-to-install-containers.md) .

## <a name="typical-workflow"></a>Tipik iş akışı

İş akışı basittir. Analiz edilecek verileri gönderir ve çıktıları kodunuzda işlersiniz. Çözümleyiciler olduğu gibi kullanılır, ek yapılandırma veya özelleştirme gerçekleştirilmez.

1. Metin Analizi için [bir Azure kaynağı oluşturun](../cognitive-services-apis-create-account.md) . Daha sonra, isteklerinizin kimlik doğrulaması için sizin için oluşturulan [anahtarı alın](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .

2. JSON biçiminde verilerinizi yapılandırılmamış ham metin olarak içeren [bir istek düzenleyin](how-tos/text-analytics-how-to-call-api.md#json-schema).

3. İstediğiniz kaynağı ekleyerek isteği kayıt sırasında oluşturulan uç noktaya: yaklaşım analizi, anahtar ifade ayıklama, dil algılama veya varlık tanımlama.

4. Yanıtın akışını yapın veya yerel ortamda depolayın. Gönderilen isteğe bağlı olarak sonuçlar yaklaşım puanı, ayıklanan anahtar ifadelerden oluşan bir koleksiyon veya dil kodu olacaktır.

Çıktı tek bir JSON belgesi olarak döndürülür ve kimlikle birlikte gönderdiğiniz tüm metin belgelerinin sonucunu içerir. Sonuçları daha sonra analiz ederek, görselleştirerek veya kategorilere ayırarak eyleme dönüştürülebilir içgörüler elde edebilirsiniz.

Veriler hesabınızda depolanmaz. Metin Analizi API'si tarafından gerçekleştirilen işlemlerde durum bilgisi yoktur. Başka bir deyişle sağladığınız metin işlenir ve sonuçlar anında döndürülür.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Birden çok programlama deneyimi düzeyi için Metin Analizi

Programlamada çok fazla deneyim olmasa bile, işlemlerinizde Metin Analizi API'si kullanmaya başlayabilirsiniz. API 'yi, deneyim düzeyinize uygun yollarla farklı şekillerde çözümlemek için nasıl kullanabileceğinizi öğrenmek için bu öğreticileri kullanın. 

* Gereken minimum programlama:
    * [Bir Yammer grubundaki yorumların yaklaşımını belirlemek için Metin Analizi API'si ve MS akışını kullanın](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Müşteri geri bildirimini çözümlemek için Power BI Metin Analizi API'si tümleştirin](tutorials/tutorial-power-bi-key-phrases.md)
* Programlama deneyimi önerilir:
    * [Azure Databricks kullanarak akış verileri üzerinde yaklaşım Analizi](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Metin çevirmek, yaklaşımı çözümlemek ve konuşmayı sentezleştirmek için bir Flask uygulaması oluşturun](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Desteklenen diller

Bu bölüm, daha kolay bulunmasını sağlama amacıyla başka bir makaleye taşınmıştır. Bu içerik için [Metin Analizi API'si desteklenen diller](text-analytics-supported-languages.md) bölümüne bakın.

<a name="data-limits"></a>

## <a name="data-limits"></a>Veri sınırları

Tüm Metin Analizi API'si uç noktaları ham metin verisi kabul eder. Geçerli sınır, her belge için 5.120 karakterdir; daha büyük belgeleri analiz etmeniz gerekiyorsa, bunları daha küçük parçalara ayırın.

| Sınır | Değer |
|------------------------|---------------|
| Tek belge için maksimum boyut | [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)ölçülen 5.120 karakter. |
| İsteğin tamamının maksimum boyutu | 1 MB |
| Bir istekte bulunabilecek maksimum belge sayısı | 1000 belge |

Oran sınırınız fiyatlandırma katmanınızda farklılık gösterecektir.

| Katman          | İstek/saniye | Dakika başına istek |
|---------------|---------------------|---------------------|
| S/Multi-Service | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

İstekler her bir Metin Analizi özelliği için ayrı olarak ölçülür. Örneğin, fiyatlandırma katmanınız için en fazla istek sayısını aynı anda her bir özelliğe gönderebilirsiniz.      

## <a name="unicode-encoding"></a>Unicode kodlama

Metin Analizi API'si, metin gösterimi ve karakter sayısı hesaplamaları için Unicode kodlamasını kullanır. İstekler UTF-8 ve UTF-16 olarak gönderilebilir, karakter sayısında fark olmayacaktır. Karakter uzunluğu için Unicode kod noktaları buluşsal değer olarak kullanılır ve metin analizi veri sınırları için eşdeğer kabul edilir. Karakter sayısını almak için [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) kullanıyorsanız, veri boyutunu ölçmek için kullandığımız yöntemi de kullanıyorsunuz.

## <a name="next-steps"></a>Sonraki adımlar

+ Uygulamalarınız için bir anahtar ve uç nokta almak üzere Metin Analizi için [bir Azure kaynağı oluşturun](../cognitive-services-apis-create-account.md) .

+ API çağrıları göndermeye başlamak için [hızlı](quickstarts/text-analytics-sdk.md) başlangıcı kullanın. Minimum kodla metin göndermeyi, analiz seçmeyi ve sonuçları görüntülemeyi öğrenin.

+ Yeni yayınlar ve özellikler hakkında bilgi edinmek için [Metin Analizi API'si](whats-new.md) yenilikleri inceleyin.

+ Azure Databricks kullanarak bu yaklaşım [Analizi öğreticisini](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) biraz daha ayrıntılı olarak inceleyin.

+ [Dış & topluluk içeriği sayfamızda](text-analytics-resource-external-community.md)diğer araç ve teknolojilerle Metin Analizi API'si nasıl kullanılacağına ilişkin blog gönderileri ve daha fazla video listesini inceleyin.

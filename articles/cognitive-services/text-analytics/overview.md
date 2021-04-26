---
title: Metin Analizi API'si-Azure bilişsel hizmetler ile metin madenciliği ve Analizi
titleSuffix: Azure Cognitive Services
description: Metin Analizi API'si ile metin madenciliği hakkında bilgi edinin. Bu uygulamayı, yaklaşım analizi, dil algılama ve diğer doğal dil Işleme biçimleri için kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/29/2021
ms.author: aahi
keywords: metin araştırma, yaklaşım analizi, metin analizi
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: b586478b6b3943fb0154ed6c50bade6fd8b08b76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219510"
---
# <a name="what-is-the-text-analytics-api"></a>Metin Analizi API'si nedir?

Metin Analizi API'si, metin madenciliği ve metin analizi için doğal dil Işleme (NLP) özellikleri sağlayan bulut tabanlı bir hizmettir: yaklaşım analizi, bakım madenciliği, anahtar tümceciği ayıklama, dil algılama ve adlandırılmış varlık tanıma.

API, geliştirme projeleriniz için bulutta makine öğrenimi ve AI algoritmaları koleksiyonu olan Azure bilişsel [Hizmetler](../index.yml)'in bir parçasıdır. Bu özellikleri, REST API [sürüm 3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V3-0/) veya [sürüm 3,1-Önizleme](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/)veya [istemci kitaplığı](quickstarts/client-libraries-rest-api.md)ile kullanabilirsiniz.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Whats-New-in-Text-Analytics-Opinion-Mining-and-Async-API/player]

Bu belge aşağıdaki makale türlerini içerir:
* [Hızlı başlangıç](./quickstarts/client-libraries-rest-api.md) , hizmete çağrı yapmanızı ve sonuçları kısa bir süre elde etmenize olanak tanıyan adım adım yönergeler. 
* [Nasıl yapılır kılavuzlarında](./how-tos/text-analytics-how-to-call-api.md) , hizmetin daha belirli veya özelleştirilmiş yollarla kullanılmasına ilişkin yönergeler bulunur.
* [Kavramlar](text-analytics-user-scenarios.md) , hizmetin işlevselliği ve özellikleri hakkında ayrıntılı açıklamalar sağlar.
* [Öğreticiler](./tutorials/tutorial-power-bi-key-phrases.md) daha fazla iş çözümlerinde bu hizmeti bir bileşen olarak nasıl kullanacağınızı gösteren kılavuzlardır.

## <a name="sentiment-analysis"></a>Yaklaşım analizi

Yaklaşım [analizini](how-tos/text-analytics-how-to-sentiment-analysis.md) kullanın ve olumlu veya olumsuz yaklaşım hakkında ipuçları için metni inceleyerek markanızı veya konuyu düşündüğünü öğrenin. 

Özelliği, bir tümce ve belge düzeyinde hizmet tarafından bulunan en yüksek güvenilirlik puanına bağlı olarak yaklaşım etiketleri ("negatif", "nötr" ve "pozitif" gibi) sağlar. Bu özellik aynı zamanda, her belge için 0 ile 1 arasında, pozitif, nötr ve negatif yaklaşım için, içindeki her bir bir tümce &. Ayrıca, hizmetini [bir kapsayıcı kullanarak](how-tos/text-analytics-how-to-install-containers.md)şirket içinde de çalıştırabilirsiniz.

V 3.1 önizlemeden başlayarak, görüşme madenciliği bir Yaklaşım Analizi özelliğidir. Doğal dil Işlemede (NLP) en boy tabanlı Yaklaşım Analizi olarak da bilinen bu özellik, kelimeyle ilgili (ürünlerin veya hizmetlerin öznitelikleri gibi) metinde daha ayrıntılı bilgi sağlar.

## <a name="key-phrase-extraction"></a>Anahtar ifade ayıklama

Metindeki ana kavramları hızlıca tanımlamak için [anahtar tümceciği ayıklama](how-tos/text-analytics-how-to-keyword-extraction.md) kullanın. Örneğin, "gıda merak ediyor ve harika personel vardı" metninde Anahtar İfade Ayıklama ana konuşmanoktaları döndürür: "yiyecek" ve "harika personel".

## <a name="language-detection"></a>Dil algılama

Dil algılama, bir [giriş metninin yazıldığı dili algılayabilir](how-tos/text-analytics-how-to-language-detection.md) ve çok çeşitli diller, çeşitler, diapacts ve bazı bölgesel/kültürel dillerinde istek üzerine gönderilen her belge için tek bir dil kodu rapor edebilir. Dil kodu bir güvenirlik puanı ile eşleştirilmiş.

## <a name="named-entity-recognition"></a>Adlandırılmış varlık tanıma

Adlandırılmış varlık tanıma (NER), kişiler, konumlar, kuruluşlar, miktarlar ve Iyi bilinen varlıkların de tanınabilmesi ve Web üzerinde daha fazla bilgi ile bağlanması halinde, metinlerinizde [varlıkları tanımlayabilir ve kategorilere ayırabilirsiniz](how-tos/text-analytics-how-to-entity-linking.md) .

## <a name="deploy-on-premises-using-docker-containers"></a>Docker kapsayıcılarını kullanarak şirket içinde dağıtma

Şirket içinde API özelliklerini dağıtmak için [metin analizi kapsayıcıları kullanın](how-tos/text-analytics-how-to-install-containers.md) . Bu Docker kapsayıcıları, uyumluluk, güvenlik veya diğer işletimsel nedenlerle hizmeti verilerinize yaklaştırmayı sağlar. Metin Analizi aşağıdaki kapsayıcıları sunmaktadır:

* yaklaşım Analizi
* anahtar tümceciği ayıklama (Önizleme)
* dil algılama (Önizleme)
* Sistem durumu için Metin Analizi (Önizleme)

## <a name="asynchronous-operations"></a>Zaman uyumsuz işlemler

`/analyze`Uç noktası, metin Analizi API'si, ner ve anahtar tümceciği ayıklama gibi seçme özelliklerini [zaman uyumsuz](how-tos/text-analytics-how-to-call-api.md)olarak kullanmanıza olanak sağlar.

## <a name="typical-workflow"></a>Tipik iş akışı

İş akışı basittir. Analiz edilecek verileri gönderir ve çıktıları kodunuzda işlersiniz. Çözümleyiciler olduğu gibi kullanılır, ek yapılandırma veya özelleştirme gerçekleştirilmez.

1. Metin Analizi için [bir Azure kaynağı oluşturun](how-tos/text-analytics-how-to-call-api.md) . Daha sonra, isteklerinizin kimlik doğrulaması için sizin için oluşturulan [anahtarı alın](how-tos/text-analytics-how-to-call-api.md) .

2. JSON biçiminde verilerinizi yapılandırılmamış ham metin olarak içeren [bir istek düzenleyin](how-tos/text-analytics-how-to-call-api.md#json-schema).

3. Kayıt sırasında, istenen kaynak: yaklaşım analizi, anahtar ifade ayıklama, dil algılama veya adlandırılmış varlık tanıma gibi bir istek noktasına isteği gönderin.

4. Yanıtın akışını yapın veya yerel ortamda depolayın. Gönderilen isteğe bağlı olarak sonuçlar yaklaşım puanı, ayıklanan anahtar ifadelerden oluşan bir koleksiyon veya dil kodu olacaktır.

Çıktı tek bir JSON belgesi olarak döndürülür ve kimlikle birlikte gönderdiğiniz tüm metin belgelerinin sonucunu içerir. Sonuçları daha sonra analiz ederek, görselleştirerek veya kategorilere ayırarak eyleme dönüştürülebilir içgörüler elde edebilirsiniz.

Veriler hesabınızda depolanmaz. Metin Analizi API'si tarafından gerçekleştirilen işlemlerde durum bilgisi yoktur. Başka bir deyişle sağladığınız metin işlenir ve sonuçlar anında döndürülür.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Birden çok programlama deneyimi düzeyi için Metin Analizi

Programlamada çok fazla deneyim olmasa bile, işlemlerinizde Metin Analizi API'si kullanmaya başlayabilirsiniz. API 'yi, deneyim düzeyinize uygun yollarla farklı şekillerde çözümlemek için nasıl kullanabileceğinizi öğrenmek için bu öğreticileri kullanın. 

* Gereken minimum programlama:
    * [Metin Analizi ve güç otomatikleştirme kullanarak Excel 'de bilgi Ayıkla](tutorials/extract-excel-information.md)
    * [Bir Yammer grubundaki yorumların yaklaşımını belirlemek için Metin Analizi API'si ve MS akışını kullanın](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Müşteri geri bildirimini çözümlemek için Power BI Metin Analizi API'si tümleştirin](tutorials/tutorial-power-bi-key-phrases.md)
* Programlama deneyimi önerilir:
    * [Azure Databricks kullanarak akış verileri üzerinde yaklaşım analizi](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Metin çevirmek, yaklaşımı çözümlemek ve konuşmayı sentezleştirmek için bir Flask uygulaması oluşturun](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Desteklenen diller

Bu bölüm, daha kolay bulunmasını sağlama amacıyla başka bir makaleye taşınmıştır. Bu içerik için [Metin Analizi API'si desteklenen diller](./language-support.md) bölümüne bakın.

<a name="data-limits"></a>

## <a name="data-limits"></a>Veri sınırları

Tüm Metin Analizi API'si uç noktaları ham metin verisi kabul eder. Daha fazla bilgi için [veri sınırları](concepts/data-limits.md) makalesine bakın.

## <a name="unicode-encoding"></a>Unicode kodlama

Metin Analizi API'si, metin gösterimi ve karakter sayısı hesaplamaları için Unicode kodlamasını kullanır. İstekler UTF-8 ve UTF-16 olarak gönderilebilir, karakter sayısında fark olmayacaktır. Karakter uzunluğu için Unicode kod noktaları buluşsal değer olarak kullanılır ve metin analizi veri sınırları için eşdeğer kabul edilir. [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements)Karakter sayısını almak için kullanırsanız, veri boyutunu ölçmek için kullandığımız yöntemi de kullanıyorsunuz.

## <a name="next-steps"></a>Sonraki adımlar

+ Uygulamalarınız için bir anahtar ve uç nokta almak üzere Metin Analizi için [bir Azure kaynağı oluşturun](../cognitive-services-apis-create-account.md) .

+ API çağrıları göndermeye başlamak için [hızlı](quickstarts/client-libraries-rest-api.md) başlangıcı kullanın. Minimum kodla metin göndermeyi, analiz seçmeyi ve sonuçları görüntülemeyi öğrenin.

+ Yeni yayınlar ve özellikler hakkında bilgi edinmek için [Metin Analizi API'si](whats-new.md) yenilikleri inceleyin.

+ Azure Databricks kullanarak bu yaklaşım [Analizi öğreticisini](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services) biraz daha ayrıntılı olarak inceleyin.

+ [Dış & topluluk içeriği sayfamızda](text-analytics-resource-external-community.md)diğer araç ve teknolojilerle Metin Analizi API'si nasıl kullanılacağına ilişkin blog gönderileri ve daha fazla video listesini inceleyin.

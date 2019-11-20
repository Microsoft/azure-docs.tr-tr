---
title: Yinelemeli uygulama tasarımı-LUSıS
titleSuffix: Azure Cognitive Services
description: Lu, bir model değişikliği, söylenişi örnekleri, yayımlama ve uç nokta sorgularından veri toplamayı en iyi şekilde öğreniyor.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487775"
---
# <a name="authoring-cycles-and-versions"></a>Yazma döngüleri ve sürümleri

LUSıS uygulamanız en iyi yinelemeli bir döngüde öğrenir:

* Yeni sürüm oluştur
* Uygulama şemasını Düzenle
    * örnek söyleyle amaçları
    * varlıklar
    * SaaS Uygulamaları Geliştirme
* eğit
* test
* publish
    * etkin öğrenme için tahmin uç noktasında test edin
* uç nokta sorgularından veri toplama

![Yazma döngüsü](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>LUSıS şeması oluşturma

Uygulama şemasının amacı, kullanıcının istediği şeyi (amaç veya amaç) ve sorunun hangi bölümlerinin yanıt belirlemesine yardımcı olan ayrıntılar (varlıklar) tanımlamaktır. 

Uygulama şemasının, ilgili sözcük ve tümceleri ve normal sözcük sıralamasını belirleyebilmeleri için uygulama etki alanlarına özel olması gerekir. 

Örnek söyleyme, uygulamanın çalışma zamanında alması beklenen Kullanıcı girişini temsil eder. 

Şema için _Amaç gerekir ve varlıklar olmalıdır_ . 

### <a name="example-schema-of-intents"></a>Örnek bir amaç şeması

En yaygın şema, amaçlar ile düzenlenmiş bir amaç şemadır. Bu tür bir şema, bir kullanıcının amaç 'ı tespit etmek için LUSıS 'e bağımlıdır. 

Bu şema türü, LUTO 'NıN amacı belirlemesine yardımcı olduysa varlık içerebilir. Örneğin, bir gönderim varlığı (bir amaca yönelik bir tanımlayıcı olarak), LUıN bir nakliye amacını belirlemesine yardımcı olur. 

### <a name="example-schema-of-entities"></a>Varlıkların örnek şeması

Bir varlık şeması, deterlerden Ayıklanacak veriler olan varlıklara odaklanır. 

Söylenişi 'in amacı, istemci uygulaması için daha az veya önemli değildir. 

Bir varlık şemasının düzenlenmesinin yaygın bir yöntemi, tüm örnek söyleylerini None amacına eklemektir. 

### <a name="example-of-a-mixed-schema"></a>Karışık şema örneği

En güçlü ve olgun şeması, eksiksiz bir varlık ve özellik aralığına sahip bir amaç şemadır. Bu şema, bir amaç veya varlık şeması olarak başlayabilir ve istemci uygulaması bu bilgi parçasına ihtiyaç duydukça her ikisinin kavramlarını dahil etmek için büyüyebilir. 

## <a name="add-example-utterances-to-intents"></a>Amaçlar için örnek ekleme

LUO 'NUN her bir **Amaç**için birkaç örnek elde etmek gerekir. Örnek söyleyler, hangi Amaçın hangi amaca yönelik olduğunu belirleyebilmek için yeterli sözcük seçimi ve sözcük sırası çeşitlemesine gerek duyar. 

> [!CAUTION]
> Toplu olarak örnek eklemeyin. 15 ila 30 ' a ve değişen örneklerle başlayın. 

Her örnek söylenişi 'in, **varlıklar**ile tasarlanıp etiketlenmesi **için gerekli verilerin** olması gerekir. 

|Anahtar öğesi|Amaç|
|--|--|
|Amaç|Kullanıcı araslarını tek bir amaç veya eylem halinde **sınıflandırın** . Örnekler `BookFlight` ve `GetWeather`içerir.|
|Varlık|Bir amaç için gereken materance 'tan veri **ayıklayın** . Örneğin, seyahat tarihi ve saati ve konumunu içerir.|

Deterlik 'in **hiçbiri** amaca uygun şekilde atanması IÇIN, lusıs uygulamanızı, uygulamanızın etki alanıyla ilgili olmayan utbotları yoksayacak şekilde tasarlayabilmeniz gerekir. 

## <a name="test-and-train-your-app"></a>Uygulamanızı test edin ve eğitme

Her bir amaca göre 15 ila 30 farklı örneğe sahip olduğunuzda, etiketli gerekli varlıklar sayesinde test ve [eğitme](luis-how-to-train.md)yapmanız gerekir. 

## <a name="publish-to-a-prediction-endpoint"></a>Tahmin uç noktasında Yayımlama

Uygulamanızı, ihtiyacınız olan [tahmin uç nokta bölgelerinde](luis-reference-regions.md) kullanılabilir olacak şekilde yayımladığınızdan emin olun. 

## <a name="test-your-published-app"></a>Yayınlanan uygulamanızı test etme

Yayınlanan LUSıS uygulamanızı HTTPS tahmin uç noktasından test edebilirsiniz. Tahmin uç noktasından test etmek, LUSıS 'nin [Gözden geçirme](luis-how-to-review-endpoint-utterances.md)için düşük güvenilirliğe sahip herhangi bir noktayı seçmesine olanak sağlar.  

## <a name="create-a-new-version-for-each-cycle"></a>Her bir döngüyle ilgili yeni bir sürüm oluşturun

LUSıS 'deki sürümler geleneksel programlama sürümlerindeki sürümlere benzerdir. Her sürüm, uygulamanın zaman içindeki bir anlık görüntüdür. Uygulamada değişiklik yapmadan önce yeni bir sürüm oluşturun. Daha eski bir sürüme dönmek daha kolay olur ve daha sonra amaçları ve kullanımları önceki bir duruma kaldırmaya çalışır.

Sürüm KIMLIĞI, karakterler, rakamlar veya '. ' karakterlerinden oluşur ve 10 karakterden uzun olamaz.

İlk sürüm (0,1) varsayılan etkin sürümdür. 

### <a name="begin-by-cloning-an-existing-version"></a>Mevcut bir sürümü klonlayarak başlayın

Yeni sürüm için başlangıç noktası olarak kullanmak üzere var olan bir sürümü kopyalayın. Bir sürümü klonladıktan sonra, yeni sürüm **etkin** sürüm olur. 

### <a name="publishing-slots"></a>Yayımlama Yuvaları
Aşama ve üretim yuvalarında yayımlayabilirsiniz. Her yuva farklı bir sürüme veya aynı sürüme sahip olabilir. Bu, robotların veya başka bir LUıN uygulama çağıran uygulamalar tarafından kullanılabilen üretime yayımlamadan önce değişiklikleri doğrulamak için yararlıdır. 

Eğitilen sürümler uygulamanızın [uç noktasında](luis-glossary.md#endpoint)otomatik olarak kullanılamaz. Uygulamanın uç noktanıza kullanılabilmesi için bir sürümü [yayımlamanız](luis-how-to-publish-app.md) veya yeniden yayımlamanız gerekir. **Hazırlama** ve **üretime**yayınlayabilirsiniz ve bu sayede, uç noktada uygulamanın iki sürümünü kullanabilirsiniz. Bir uç noktada uygulamanın daha fazla sürümü varsa, sürümü dışarı aktarıp yeni bir uygulamaya yeniden içeri aktarmanız gerekir. Yeni uygulamanın farklı bir uygulama KIMLIĞI vardır.

### <a name="import-and-export-a-version"></a>Bir sürümü içeri ve dışarı aktarma
Uygulama düzeyinde bir sürümü içeri aktarabilirsiniz. Bu sürüm etkin sürüm olur ve uygulama dosyasının `versionId` özelliğindeki sürüm KIMLIĞINI kullanır. Ayrıca, sürüm düzeyinde mevcut bir uygulamaya de aktarabilirsiniz. Yeni sürüm etkin sürüm olur. 

Bir sürümü uygulama veya sürüm düzeyinde dışa aktarabilirsiniz. Tek fark, uygulama düzeyinde dışarı aktarılmış sürümün sürüm düzeyinde şu anda etkin olan sürümüdür, **[Ayarlar](luis-how-to-manage-versions.md)** sayfasında dışarı aktarılacak herhangi bir sürümü seçebilirsiniz. 

İçe aktarılmış dosya şunları içermez:

* makine öğrenilmiş bilgiler, uygulama alındıktan sonra geri çekme yaptığından
* katkıda bulunan bilgileri

LUSıS uygulama şemanızı yedeklemek için, bir sürümü LUSıS portalından dışarı aktarın.

## <a name="manage-contributor-changes-with-versions-and-apps"></a>Sürümler ve uygulamalarla katkıda bulunan değişiklikleri yönetme

LUO, Azure Kaynak düzeyi izinleri sunarak bir uygulamanın katkı sağlayanlar kavramını sağlar. Hedeflenen işbirliği sağlamak için bu kavramı sürümü oluşturma ile birleştirin. 

Uygulamanıza katkıda bulunan değişiklikleri yönetmek için aşağıdaki teknikleri kullanın.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Aynı uygulama içinde birden çok sürümü yönetme
Her yazar için bir temel sürümden [kopyalamaya](luis-how-to-manage-versions.md#clone-a-version)göre başlayın. 

Her yazar uygulamanın kendi sürümünde değişiklik yapar. Her yazar modeliyle karşılandıktan sonra, yeni sürümleri JSON dosyalarına dışarı aktarın.  

Aktarılmış uygulamalar,. JSON veya. lu dosyaları, değişiklikler için karşılaştırılabilir. Yeni sürümden tek bir dosya oluşturmak için dosyaları birleştirin. **VersionId** özelliğini yeni birleştirilmiş sürümü işaret etmek üzere değiştirin. Bu sürümü özgün uygulamaya aktarın. 

Bu yöntem, bir etkin sürüme, bir aşama sürümüne ve bir yayımlanmış sürüme sahip etmenize olanak tanır. Etkin sürüm ile ilgili sonuçları, [etkileşimli test bölmesindeki](luis-interactive-test.md)yayımlanmış bir sürümle (aşama veya üretim) karşılaştırabilirsiniz.

### <a name="manage-multiple-versions-as-apps"></a>Birden çok sürümü uygulama olarak yönetme
Temel sürümü [dışarı aktarın](luis-how-to-manage-versions.md#export-version) . Her yazar sürümü içeri aktarır. Uygulamayı içeri aktaran kişi, sürümün sahibidir. Uygulamayı değiştirme bittiğinde sürümü dışarı aktarın. 

Dışarı aktarılmış uygulamalar, değişiklikler için temel dışarı aktarmaya kıyasla JSON biçimli dosyalardır. Yeni sürümden tek bir JSON dosyası oluşturmak için dosyaları birleştirin. JSON içindeki **VersionId** özelliğini yeni birleştirilmiş sürümü işaret etmek üzere değiştirin. Bu sürümü özgün uygulamaya aktarın.

[Ortak çalışanlarla](luis-how-to-collaborate.md)ilgili katkıları yazma hakkında daha fazla bilgi edinin.

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>Yeni yazma döngüsünü başlatmak için uç nokta utslerini gözden geçirin

Bir yazma döngüsüyle işiniz bittiğinde, yeniden başlatabilirsiniz. [İnceleme tahmini uç noktası utterations](luis-how-to-review-endpoint-utterances.md) lu, düşük güvenilirlikle işaretlendi. Bu söyleyeni, hem doğru tahmini amaç hem de doğru ve tamamen ayıklanan varlık için denetleyin. Değişiklikleri gözden geçirdikten ve kabul ettikten sonra, gözden geçirme listesinin boş olması gerekir.  

## <a name="next-steps"></a>Sonraki adımlar

[İşbirliği](luis-concept-keys.md)hakkında kavramları öğrenin.

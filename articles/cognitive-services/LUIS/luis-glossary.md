---
title: Sözlük-LUSıS
description: Sözlük, LUSıS API hizmetiyle çalışırken karşılaşabileceğiniz terimleri açıklar.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 7c65c8272172cab9f5361d16141bf7b229037480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98786953"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Dil ortak Sözlük ve kavramların sözlüğünü anlama
Language Understanding (LUSıS) sözlüğü, LUO hizmeti ile çalışırken karşılaşabileceğiniz terimleri açıklar.

## <a name="active-version"></a>Etkin sürüm

Etkin sürüm, bu, [' ın,](luis-how-to-manage-versions.md) ,,,,,,,,,,,,,,,,,,,,,, LUU portalında, etkin sürüm olmayan bir sürümde değişiklik yapmak istiyorsanız, önce bu sürümü etkin olarak ayarlamanız gerekir.

## <a name="active-learning"></a>Etkin öğrenme

Etkin öğrenme, etikete yönelik bilgilendirici yeni örnekleri belirlemek için makine öğrenimi modelinin kullanıldığı makine öğrenimi tekniğidir. LUSıS 'de, etkin öğrenme, geçerli tahminleri modelinize yönelik olmayan uç nokta trafiğinden elde edilen noktaları eklemek anlamına gelir. Etiketle ilgili olarak göstermek için "Endpoint deterlerini gözden geçir" seçeneğine tıklayın.

Ayrıca bkz:
* [Kavramsal bilgiler](luis-concept-review-endpoint-utterances.md)
* [Uç nokta utlerini gözden geçirme öğreticisi](luis-tutorial-review-endpoint-utterances.md)
* [Uç nokta utbotları Inceleyerek](luis-how-to-review-endpoint-utterances.md) Luo uygulamasını geliştirme

## <a name="application-app"></a>Uygulama (uygulama)

LUSıS, uygulamanız veya uygulamanız, belirli bir senaryo için amaçları ve varlıkları tahmin etmek üzere birlikte çalışarak, aynı veri kümesi üzerine inşa edilen makine tarafından öğrenilen modeller koleksiyonudur. Her uygulamanın ayrı bir tahmin uç noktası vardır.

Bir IK bot oluşturuyorsanız, tek bir uygulamada gruplandırdığınızda "zamanlama saati", "avantajları hakkında bilgi sorgula" ve "kişisel bilgileri Güncelleştir" gibi bir amaç kümesine sahip olabilirsiniz.

## <a name="authoring"></a>Yazma

Yazma, LUıS portalını veya yazma API 'Lerini kullanarak bir LUıS uygulaması oluşturma, yönetme ve dağıtma olanağıdır.

### <a name="authoring-key"></a>Yazma anahtarı

[Yazma anahtarı](luis-how-to-azure-subscription.md) , uygulamayı yazmak için kullanılır. Üretim düzeyi uç nokta sorguları için kullanılmaz. Daha fazla bilgi için bkz. [anahtar sınırları](luis-limits.md#key-limits).

### <a name="authoring-resource"></a>Kaynak yazma

LUSıS [Authoring Resource](luis-how-to-azure-subscription.md#azure-resources-for-luis) , Azure aracılığıyla kullanılabilen yönetilebilir bir öğedir. Kaynak, Azure hizmetinin ilişkili yazma, eğitim ve yayımlama becerileriyle olan erişimdir. Kaynak, ilişkili Azure hizmetine erişmeniz için gereken kimlik doğrulaması, yetkilendirme ve güvenlik bilgilerini içerir.

Yazma kaynağının bir Azure "türü" vardır `LUIS-Authoring` .

## <a name="batch-test"></a>Batch testi

Toplu iş testi, geçerli bir LUO uygulamasının modellerini tutarlı ve bilinen bir kullanıcı tarafından yapılan test kümesiyle doğrulama yeteneğidir. Batch testi, [JSON biçimli bir dosyada](./luis-how-to-batch-test.md#batch-test-file)tanımlanır.


Ayrıca bkz:
* [Kavramlar](./luis-how-to-batch-test.md)
* Batch [testini çalıştırma](luis-how-to-batch-test.md)
* [Öğretici](./luis-how-to-batch-test.md) -Batch testi oluşturma ve çalıştırma

### <a name="f-measure"></a>F ölçümü

Batch testinde, testin doğruluğunu ölçer.

### <a name="false-negative-fn"></a>Yanlış negatif (FN)

Toplu iş testinde, veri noktaları, uygulamanızın hedef amaç/varlık yokluğunu yanlış tahmin ettiği noktaları temsil eder.

### <a name="false-positive-fp"></a>Yanlış pozitif (FP)

Toplu iş testinde, veri noktaları, uygulamanızın hedef amaç/varlık varlığını yanlış tahmin ettiği noktaları temsil eder.

### <a name="precision"></a>Duyarlık
Toplu iş testinde duyarlık (pozitif tahmine dayalı değer olarak da bilinir), alınan bildirimler arasında ilgili söylerin kesiri olur.

Anhayvan Batch testi için bir örnek, toplam hayvanlar sayısı (koyun ve koyun) ile ayrılmış olan koyun sayısıdır.

### <a name="recall"></a>Geri çağırma

Toplu iş testinde geri çek (duyarlılık olarak da bilinir), luto Genelleştirmenin bir özelliğidir.

Bir anhayvan Batch testi için bir örnek, kullanılabilir olan toplam koyun tarafından ayrılmış olan koyun sayısıdır.

### <a name="true-negative-tn"></a>True negatif (TN)

Doğru negatif bir değer, uygulamanızın hiçbir eşleşme olmadığını doğru bir şekilde tahmin ediyor. Toplu iş testinde, uygulamanız bu amaca veya varlığa etiketli bir örnek için bir amaç veya varlık tahmin ederken doğru bir negatif gerçekleşir.

### <a name="true-positive-tp"></a>True pozitif (TP)

True pozitif (TP) doğru pozitif bir değer, uygulamanız bir eşleşmeyi doğru bir şekilde tahmin ediyor. Toplu iş testinde, uygulamanız bu amaca veya varlığa etiketli bir örnek için bir amaca veya varlığa tahmin edildiğinde doğru pozitif bir durum oluşur.

## <a name="classifier"></a>Sınıflandırıcı

Sınıflandırıcı, bir girişin hangi kategoriye veya sınıfa uyduğunu tahmin eden, makine tarafından öğrenilen bir modeldir.

[Amaç](#intent) , sınıflandırıcının bir örneğidir.

## <a name="collaborator"></a>İşbirlikçi

Ortak çalışan, kavramsal olarak aynı şeyi [yapar.](#contributor) Bir sahip, ortak çalışan e-posta adresini Azure rol tabanlı erişim denetimi (Azure RBAC) ile denetlenen bir uygulamaya eklediğinde ortak çalışan erişimi verilir. Ortak çalışanları hala kullanıyorsanız, LUSıS hesabınızı geçirmeniz ve Azure RBAC ile katılımcıları yönetmek için LUSıS yazma kaynaklarını kullanmanız gerekir.

## <a name="contributor"></a>Katılımcı

Katkıda bulunan, uygulamanın [sahibi](#owner) değildir, ancak hedefleri, varlıkları, utbotları eklemek, düzenlemek ve silmek için aynı izinlere sahiptir. Katkıda bulunan, bir Lua uygulamasına Azure rol tabanlı erişim denetimi (Azure RBAC) sağlar.

Ayrıca bkz:
* Katkıda [bulunanlar ekleme](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)

## <a name="descriptor"></a>CI

Tanımlayıcı, daha önce bir makine öğrenimi [özelliği](#features)için kullanılan terimdir.

## <a name="domain"></a>Etki alanı

LUSıS bağlamında, etki alanı bir bilgi alanıdır. Etki alanınız senaryonuz için özeldir. Farklı etki alanları, etki alanı bağlamında anlamı olan belirli dili ve terimleri kullanır. Örneğin, müzik çalmak için bir uygulama oluşturuyorsanız, uygulamanız müziğe özgü hüküm ve dile özgü olacaktır: "şarkı, izleme, albüm, şarkı sözleri, b-tarafı, sanatçı" gibi kelimeler. Etki alanı örnekleri için bkz. [önceden oluşturulmuş etki alanları](#prebuilt-domain).

## <a name="endpoint"></a>Uç Nokta

### <a name="authoring-endpoint"></a>Yazma uç noktası

LUSıS yazma uç noktası URL 'SI, uygulamanızı yazdığınız, eğitebileceğiniz ve yayımladığınız yerdir. Uç nokta URL 'SI, yayımlanan uygulamanın bölge veya özel alt etki alanının yanı sıra uygulama KIMLIĞINI içerir.

[Geliştirici başvurusundan](developer-reference-resource.md#rest-endpoints) uygulamanızı programlama yoluyla yazma hakkında daha fazla bilgi edinin

### <a name="prediction-endpoint"></a>Tahmin uç noktası

Lusıs tahmin uç noktası URL 'SI, luın [uygulaması](#application-app) yapıldıktan ve YAYıMLANDıKTAN sonra lusıs sorgularını gönderdiğiniz yerdir. Uç nokta URL 'SI, yayımlanan uygulamanın bölge veya özel alt etki alanının yanı sıra uygulama KIMLIĞINI içerir. Uç noktasını uygulamanızın **[Azure kaynakları](luis-how-to-azure-subscription.md)** sayfasında bulabilir veya [Get App Info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API 'sinden Endpoint URL 'sini alabilirsiniz.

Tahmin uç noktasına erişiminiz, LUSıS tahmin anahtarıyla yetkilendirildi.

## <a name="entity"></a>Varlık

[Varlıklar](luis-concept-entity-types.md) , amacı karşılamak veya tanımlamak için kullanılan bilgileri tanımlayan sözcüklerdir. Varlığınız karmaşıktır ve modelinizin belirli bölümleri belirlemesini istiyorsanız modelinizi alt varlıklara kesebilirsiniz. Örneğin, bir adresi tahmin etmek ve ayrıca cadde, şehir, eyalet ve zipcode 'un alt varlıklarını kullanmak isteyebilirsiniz. Varlıklar, modellerdeki özellikler olarak da kullanılabilir. LUSıS uygulamasındaki yanıtınız hem tahmin edilen amaçları hem de tüm varlıkları içerir.

### <a name="entity-extractor"></a>Varlık ayıklayıcısı

Bir varlık ayıklayıcısı bazen yalnızca bir ayıklayıcısı olarak bilinen, LUıN varlıkları tahmin etmek için kullandığı makine tarafından öğrenilen modelin türüdür.

### <a name="entity-schema"></a>Varlık şeması

Varlık şeması, alt varlıklar ile makine tarafından öğrenilen varlıklar için tanımladığınız yapısıdır. Tahmin uç noktası, şemada tanımlanan ayıklanan varlıkların ve alt varlıkların tümünü döndürür.

### <a name="entitys-subentity"></a>Varlığın alt varlığı

Alt varlık, makine öğrenimi varlığının alt varlıktır.

### <a name="non-machine-learning-entity"></a>Makine öğrenimi olmayan varlık

Veri ayıklamak için metin eşleştirmeyi kullanan bir varlık:
* Liste varlığı
* Normal ifade varlığı

### <a name="list-entity"></a>Liste varlığı

[Liste varlığı](reference-entity-list.md) , sabit ve kapalı bir ilgili sözcük kümesini eş anlamlılarıyla birlikte temsil eder. Liste varlıkları, öğrenilen öğrenilen varlıkların aksine tam eşleştirmelere sahiptir.

Liste varlığındaki bir sözcük listeye dahil edildiğinde varlık tahmin edilir. Örneğin, "Boyut" adlı bir liste varlığınız varsa ve listede "küçük, orta, büyük" kelimeleriniz varsa, "küçük", "Orta" veya "büyük" sözcüklerinin bağlam ne olursa olsun kullanıldığı tüm bildirimler için boyut varlığı tahmin edilir.

### <a name="regular-expression"></a>Normal ifade

[Normal ifade varlığı](reference-entity-regular-expression.md) bir normal ifadeyi temsil eder. Normal ifade varlıkları, öğrenilen öğrenilen varlıkların aksine tam eşleştirmelere sahiptir.
### <a name="prebuilt-entity"></a>Önceden oluşturulmuş varlık

[Önceden oluşturulmuş varlık](#prebuilt-entity) Için önceden oluşturulmuş model girişine bakın

## <a name="features"></a>Özellikler

Machine Learning 'de bir özellik, modelin belirli bir kavramı tanımasına yardımcı olan bir özelliktir. BASıS 'nin kullanabileceği ancak sabit bir kural kullanmayan bir ipucu.

Bu terim **[makine öğrenimi özelliği](luis-concept-feature.md)** olarak da adlandırılır.

Bu ipuçları, yeni verilerin nasıl önleyeceğinizi öğrenmek için etiketlerle birlikte kullanılır. LUSıS her iki ifade listesini destekler ve diğer modelleri özellikler olarak kullanmaktır.

### <a name="required-feature"></a>Gerekli özellik

Gerekli bir özellik, bir LUSıS modelinin çıkışını kısıtlamak için bir yoldur. Bir varlık için bir özellik gerekli olarak işaretlendiğinde, makinenin tahmin edilebileceği makinede ne olursa olsun özelliğin tahmin edilebilir olması gerekir.

Bir menü sıralaması bot için miktar varlığında gerekli olarak işaretlediğiniz önceden oluşturulmuş bir özelliği olan bir örnek düşünün. Botunuzun gördüğü zaman, `I want a bajillion large pizzas?` bajilon, göründüğü bağlamdan bağımsız olarak tahmin edilmez. Bajilon geçerli bir sayı değil ve önceden oluşturulmuş varlık sayısı tarafından tahmin edilmez.

## <a name="intent"></a>Amaç

[Amaç](luis-concept-intent.md) , kullanıcının gerçekleştirmek istediği bir görevi veya eylemi temsil eder. Bu, bir kullanıcının girişinde ifade edilen, bir uçuş veya fatura ödeme gibi bir amaç veya hedeftir. Luin, bir bütün olarak bir amaç olarak sınıflandırıldı, ancak söylenişi 'in bazı parçaları varlık olarak ayıklanır

## <a name="labeling-examples"></a>Etiketleme örnekleri

Etiketleme veya işaret, pozitif veya negatif bir örneği bir modelle ilişkilendirme işlemidir.

### <a name="labeling-for-intents"></a>Amaçlar için etiketleme
LUSıS 'de, bir uygulama içindeki amaçlar birbirini dışlar. Bu, bir amaca göre bir değer eklediğinizde, bu amaç için _pozitif_ bir örnek ve diğer tüm amaçlar için _negatif_ bir örnek olarak değerlendirilir. Negatif örnekler, uygulamanın kapsamı dışında olan utusları temsil eden "none" amacı ile karıştırılmamalıdır.

### <a name="labeling-for-entities"></a>Varlıklar için etiketleme
LUSıS 'de, bir amaca veya tümceciği, bir varlıkla birlikte _pozitif_ bir örnek olarak [etiketleyin](label-entity-example-utterance.md) . Etiketleme amacı, bu söylemenin ne tahmin etmesi gerektiğini gösterir. Etiketli utterslar, amacı eğitemak için kullanılır.

## <a name="luis-app"></a>LUSıS uygulaması

[Uygulama tanımına (uygulama)](#application-app)bakın.

## <a name="model"></a>Modelleme

(Makine tarafından öğrenilen) modeli, giriş verilerini tahmin eden bir işlevdir. LUO 'da, amaç sınıflandırıcıları ve varlık ayıklayıcıları genel olarak "modeller" olarak ifade edilir ve "uygulama" olarak bir araya göre eğitilen, yayınlanan ve sorgulanan bir model koleksiyonuna başvurduk.

## <a name="normalized-value"></a>Normalleştirilmiş değer

[Liste](#list-entity) varlıklarınızın değerlerini eklersiniz. Bu değerlerin her biri bir veya daha fazla eş anlamlı listesine sahip olabilir. Yanıtta yalnızca normalleştirilmiş değer döndürülür.

## <a name="overfitting"></a>Fazla uygunluk

Fazla sığdırma, model belirli örneklerde düzeltilebilir ve iyi genelleştiritemediğinde gerçekleşir.

## <a name="owner"></a>Sahip

Her uygulamanın, uygulamayı oluşturan kişi olan bir sahibi vardır. Sahibi Azure portal uygulama için izinleri yönetir.

## <a name="phrase-list"></a>Tümcecik listesi

[Tümcecik listesi](luis-concept-feature.md) , aynı sınıfa ait olan ve benzer şekilde (örneğin, şehirlerin veya ürünlerin adları) bir grup (örneğin, şehir veya ürün adları) içeren belirli bir makine öğrenimi özelliği türüdür.

## <a name="prebuilt-model"></a>Önceden oluşturulmuş model

[Önceden oluşturulmuş bir model](luis-concept-prebuilt-model.md) , etiketli örneklerle birlikte hem bir amaç, varlık ya da koleksiyonudur. Uygulamanız için gereken model geliştirme işini azaltmak için, bu ortak önceden oluşturulmuş modeller uygulamanıza eklenebilir.

### <a name="prebuilt-domain"></a>Önceden oluşturulmuş etki alanı

Önceden oluşturulmuş bir etki alanı, giriş Otomasyonu (HomeAutomation) veya restoran ayırmaları (Restoran Antreservatıon) gibi belirli bir etki alanı için yapılandırılmış bir LUL uygulamasıdır. Bu etki alanı için amaçlar, utbotslar ve varlıklar yapılandırılır.

### <a name="prebuilt-entity"></a>Önceden oluşturulmuş varlık

Önceden oluşturulmuş bir varlık, sayı, URL ve e-posta gibi yaygın bilgi türleri için bir LUSıS sağlar. Bunlar, genel veriler temel alınarak oluşturulur. Önceden oluşturulmuş bir varlığı tek başına bir varlık olarak veya bir varlığa bir özellik olarak eklemeyi tercih edebilirsiniz

### <a name="prebuilt-intent"></a>Önceden oluşturulan amaç

Önceden oluşturulmuş bir amaç, yaygın bilgi türleri sağlar ve kendi etiketli örnek türlerinde yer almaktadır.

## <a name="prediction"></a>Tahmin

Tahmin, yeni verileri alan (Kullanıcı utterance) Azure LUSıS tahmin hizmetine yönelik bir REST isteği ve hangi amaçlardan ve varlıkların bulunduğunu belirlemek için bu verilere eğitilen ve yayımlanmış uygulamayı uygular.

### <a name="prediction-key"></a>Tahmin anahtarı

[Tahmin anahtarı](luis-how-to-azure-subscription.md) (daha önce abonelik anahtarı olarak bilinirdi), Azure 'da, tahmin uç noktasının kullanımını YETKILENDIREN bir halsıs hizmeti ile ilişkili anahtardır.

Bu anahtar yazma anahtarı değil. Tahmin uç noktası anahtarınız varsa, yazma anahtarı yerine herhangi bir uç nokta isteği için kullanılmalıdır. Geçerli tahmin anahtarınızı, LUSıS Web sitesinde Azure kaynakları sayfasının en altında bulunan uç nokta URL 'SI içinde görebilirsiniz. Bu, abonelik anahtar adı/değer çiftinin değeridir.

### <a name="prediction-resource"></a>Tahmin kaynağı

LUSıS tahmin kaynağınız, Azure aracılığıyla kullanılabilen yönetilebilir bir öğedir. Kaynak, Azure hizmeti 'nin ilişkili tahminiyle olan erişimdir. Kaynak tahminleri içerir.

Tahmin kaynağında bir Azure "Kind" bulunur `LUIS` .

### <a name="prediction-score"></a>Tahmin puanı

[Puan](luis-concept-prediction-score.md) , 0 ile 1 arasında bir sayıdır ve sistemin belirli bir girişin belirli bir amaca uyanın ne kadar iyi olduğunu gösteren bir ölçüdür. 1 ' e yakın bir puan, sistem çıktısı hakkında çok önemlidir ve 0 ' a yakın bir puan, sistemin girişin belirli bir çıktıyla eşleşmediğinden emin olduğu anlamına gelir. Ortadaki puanlar, sistemin kararı nasıl yapacağınızdan çok büyük bir anlama gelir.

Örneğin, bir müşteri metninin bir yiyecek siparişi içerip içerne zaman dahil olduğunu belirlemek için kullanılan bir model alın. "Bir kahve siparişi vermek istiyorum" (sistem bunun bir sıraya ait olduğu çok önemlidir) ve "ekibim oyunu geçen gece oyunu kazandı" (sistem bu bir sıra DEĞIL) için 0 puanı verebilir. Ayrıca, "bir Tea bakalım" (bir sıra olduğundan emin değil) için 0,5 Puanı bulunabilir.

## <a name="programmatic-key"></a>Programlı anahtar

[Yazma anahtarı](#authoring-key)olarak yeniden adlandırıldı.

## <a name="publish"></a>Yayımlama

[Yayımlama](luis-how-to-publish-app.md) , bir lusıs etkin sürümünü hazırlama veya üretim [uç noktasında](#endpoint)kullanılabilir hale getirmek anlamına gelir.

## <a name="quota"></a>Kota

LUSıS kotası, Azure abonelik katmanının kısıtlamasıdır. LUIN kotası, her saniyedeki istek (HTTP durumu 429) ve bir ayda toplam istek (HTTP durumu 403) ile sınırlandırılabilir.

## <a name="schema"></a>Şema

Şemanız, alt varlıkların yanı sıra amaçları ve varlıklarınızı içerir. Şema başlangıçta zaman içinde tekrarlandırılır. Şema uygulama ayarları, özellikler veya örnek söyleylerini içermez.

## <a name="sentiment-analysis"></a>Yaklaşım Analizi
Yaklaşım analizi, [metin analizi](../text-analytics/overview.md)tarafından sağlanan sensülebilirlik değerlerinin pozitif veya negatif değerlerini sağlar.

## <a name="speech-priming"></a>Konuşma primi

Konuşma tanıma, senaryolarınız için [konuşma hizmetleriyle](../speech-service/overview.md)yaygın olarak kullanılan sözlü sözcüklerin ve tümceciklerin tanınmasını geliştirir. Konuşma özelliği etkin uygulamalar için, bu belirli uygulama için özelleştirilmiş bir konuşma modeli oluşturarak konuşma tanıma doğruluğunu artırmak için tüm LUYA etiketli örnekler kullanılmıştır. Örneğin, bir Chess oyununda, Kullanıcı "kgeceleri" olarak söylediğinizden emin olmak istiyorsanız, "gece taşı" olarak yorumlanmaz. LUO uygulaması, "kgece" ın bir varlık olarak etiketlendiği örnekleri içermelidir.

## <a name="starter-key"></a>Başlangıç anahtarı

İlk olarak lusıs kullanarak ilk kez başlatıldığında kullanılacak olan ücretsiz bir anahtar.

## <a name="synonyms"></a>Eş Anlamlı Sözcükler

LUıN [listesi varlıkları](reference-entity-list.md)' nda, her birinin bir eş anlamlı listesine sahip olabilen normalleştirilmiş bir değer oluşturabilirsiniz. Örneğin, küçük, orta, büyük ve çok büyük normalleştirilmiş değerler içeren bir boyut varlığı oluşturursanız. Şöyle her bir değer için eş anlamlı oluşturabilirsiniz:

|Önemli değer| Eş Anlamlı Sözcükler|
|--|--|
|Küçük| küçük bir tane, 8 ons|
|Orta| normal, 12 ons|
|Büyük| büyük, 16 ons|
|Xtra büyük| en büyük bir 24 ons|

Girişte herhangi bir eş anlamlılar bulunduğunda, model varlık için normalleştirilmiş değeri döndürür.

## <a name="test"></a>Test

Bir lusıs uygulamasını [test etmek](luis-concept-test.md) , model tahminlerinin görüntülenmesi anlamına gelir.

## <a name="timezone-offset"></a>Saat dilimi kayması

Uç nokta [Timezonesapmayı](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)içerir. Bu sayı, datetimeV2 önceden oluşturulmuş varlığa eklemek veya kaldırmak istediğiniz dakika cinsinden sayıdır. Örneğin, söylenişi "Şimdi ne zaman gelir?" ise, döndürülen datetimeV2 istemci isteği için geçerli süredir. İstemci isteğiniz, bot 'un kullanıcısı ile aynı olmayan bir bot veya başka bir uygulamadan geliyorsa, bot ve Kullanıcı arasındaki sapmayı geçirmeniz gerekir.

Bkz. [önceden oluşturulmuş datetimeV2 varlığının saat dilimini değiştirme](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Belirteç
[Belirteç](luis-language-support.md#tokenization) , luın tanıyabileceği en küçük metin birimidir. Bu, dillerin genelinde biraz farklılık gösterir.

**İngilizce** için, belirteç ve sayıların sürekli bir yayılımı (boşluk veya noktalama işareti yoktur). Boşluk bir belirteç DEĞIL.

|İfade|Belirteç sayısı|Açıklama|
|--|--|--|
|`Dog`|1|Noktalama veya boşluk içermeyen tek bir sözcük.|
|`RMT33W`|1|Kayıt Bulucu numarası. Sayı ve harflerden oluşabilir, ancak herhangi bir noktalama işareti yoktur.|
|`425-555-5555`|5|Telefon numarası. Her bir noktalama işareti tek bir belirteçtir, bu nedenle  `425-555-5555` 5 belirteç olacaktır:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Eğitim

Eğitim süreci, son eğitiminden bu yana etkin sürümde yapılan tüm değişiklikler hakkında [eğitme](luis-how-to-train.md) işlemidir.

### <a name="training-data"></a>Eğitim verileri

Eğitim verileri, bir modeli eğitmek için gereken bilgi kümesidir. Bu, başvuru, Özellikler ve uygulama ayarları etiketli şemayı içerir.

### <a name="training-errors"></a>Eğitim hataları

Eğitim hataları, kendi etiketleriyle eşleşmeyen eğitim verilerinizde tahminlerdir.

## <a name="utterance"></a>İfade

[Söylenişi](luis-concept-utterance.md) , konuşmadaki bir tümcenin kısa metin temsilcisi olan kullanıcı girişi olur. "Yeni bir sonraki Salı için kitap 2 bileti" gibi doğal bir dil tümceciktir. Model eğitimi için örnek söyleymeleri eklenmiştir ve model, çalışma zamanında yeni söylenişi üzerinde tahmin edilir

## <a name="version"></a>Sürüm

Bir lusıs [sürümü](luis-how-to-manage-versions.md) , bir LUSıS uygulama kimliğiyle ve yayımlanan uç noktayla ILIŞKILI bir lusıs uygulamasının belirli bir örneğidir. Her LUSıS uygulamasının en az bir sürümü vardır.

---
title: Sık sorulan sorular (SSS)-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu makale, Language Understanding (LUIS) hakkında sık sorulan soruların yanıtlarını içerir.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: a2472064720af0a25568a2f173b971898b1f2e25
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123105"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding sık sorulan sorular (SSS)

Bu makale, Language Understanding (LUIS) hakkında sık sorulan soruların yanıtlarını içerir.

## <a name="whats-new"></a>Yenilikler

Language Understanding (LUSıS) yenilikleri hakkında [daha fazla bilgi edinin](whats-new.md) .

<a name="luis-authoring"></a>

## <a name="authoring"></a>Yazma

### <a name="what-are-the-luis-best-practices"></a>LUIS en iyi uygulamalar nelerdir?
İle başlayan [geliştirme döngüsü](luis-concept-app-iteration.md), ardından okuma [en iyi uygulamalar](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>LUIS uygulama oluşturmaya başlamak için en iyi yolu nedir?

Uygulamanızı oluşturmak için en iyi yollarından biri sayesinde bir [artımlı işlem](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Uygulamamın ıntents modellemek için iyi bir uygulama nedir? Daha özel ya da daha genel bir ıntents oluşturmalıyım?

Çakışan, ancak bu nedenle özel olmayan, benzer amaçları arasında ayrım yapmak LUIS zorlaştırır emin olacak şekilde genel olmayan hedefleri seçin. Discriminative belirli hedefleri oluşturma LUIS modelleme için en iyi biridir.

### <a name="is-it-important-to-train-the-none-intent"></a>Hiçbiri hedefi eğitmek önemlidir?

Evet, eğitmek iyi olduğu, **hiçbiri** diğer amaçlar için daha fazla etiket ekledikçe daha fazla Konuşma ile hedefi. 1 veya 2 etiketleri eklenen iyi oranıdır **hiçbiri** bir amaç için eklenen her 10 etiketler. Bu oran LUIS discriminative gücünü artırıyor.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Konuşma, yazım hatalarını düzeltmek nasıl?

Bkz: [Bing yazım denetimi API'si V7](luis-tutorial-bing-spellcheck.md) öğretici. LUIS, Bing yazım denetimi API'si V7 tarafından uygulanan sınırları zorlar.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>LUIS uygulamamı program aracılığıyla nasıl düzenleyebilirim?
Program aracılığıyla LUIS uygulamanızı düzenlemek için kullanın [yazma API](https://go.microsoft.com/fwlink/?linkid=2092087). Bkz: [API geliştirme LUIS çağrı](./get-started-get-model-rest-apis.md) ve [Node.js kullanarak program aracılığıyla LUIS uygulaması oluşturma](./luis-tutorial-node-import-utterances-csv.md) yazma API'nin nasıl çağrılacağını örnekleri için. Yazma API kullanmanızı gerektirir. bir [anahtar yazma](luis-concept-keys.md#azure-resources-for-luis) yerine bir uç noktası anahtarı. Programlı yazma, ayda en fazla 1.000.000 çağrısı ve beş saniyede sağlar. LUIS ile kullandığınız anahtarları hakkında daha fazla bilgi için bkz. [anahtarları Yönet](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Burada sağlanan normal ifade deseni özelliği eşleşiyor mu?
Önceki **deseni özelliği** şu anda kullanım dışı, değiştirilen  **[desenleri](luis-concept-patterns.md)** .

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Bir varlık doğru veri çekmek için nasıl kullanırım?
Bkz: [varlıkları](luis-concept-entity-types.md) ve [veri ayıklama](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Bir örnek utterance çeşitleri noktalama dahil edilsin mi?
Amaç için örnek konuşma olarak farklı çeşitlemeleri ekleyebilir veya ekleme ile örnek utterance desenini [yok saymak için söz dizimi](luis-concept-patterns.md#pattern-syntax) noktalama işareti.

### <a name="does-luis-currently-support-cortana"></a>LUIS, şu anda cortana'yı destekliyor mu?

Cortana önceden oluşturulmuş uygulamalar, 2017'de kullanım dışı bırakıldı. Bunlar artık desteklenir.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Bir LUIS uygulaması sahipliğini nasıl aktarabilir?
Bir LUIS uygulaması için farklı bir Azure aboneliği aktarmayı LUIS uygulaması dışarı aktarma ve yeni bir hesap kullanarak içe aktarın. Çağıran istemci uygulamasındaki LUIS uygulama kodunu güncelleştirin. Yeni uygulamayı biraz daha farklı LUIS özgün uygulamadan puanları döndürebilir.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Önceden oluşturulmuş bir varlık özel varlığım yerine örnek bir şekilde etiketlidir. Bu Nasıl yaparım? düzeltilsin mi? 

Luu portalında, ayıklama işlemi sırasında ilgilendiğiniz tam varlığın metnini etiketleyebilir. Lua portalı doğru varlık tahminini göstermiyorsa, daha fazla işlem eklemeniz ve varlığın metin içinde etiketlenmesi veya bir tanımlayıcı eklemeniz (örn. bir özellik gibi) gerekebilir. 

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Bir uygulama veya sürüm dosyasını içeri aktarmaya çalıştım, ancak bir hata oluştu, ne oldu? 

[Sürüm içeri aktarma hataları](luis-how-to-manage-versions.md#import-errors)hakkında daha fazla bilgi edinin.

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>İşbirliği ve katkıda bulunma

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Nasıl yaparım?, Azure Active Directory (Azure AD) veya rol tabanlı erişim denetimi (RBAC) ile BASıS erişimi sağlar mi?

Ortak çalışanlarla erişim verme hakkında bilgi edinmek için bkz. [Azure Active Directory kaynakları](luis-how-to-collaborate.md#azure-active-directory-resources) ve [Azure Active Directory Kiracı Kullanıcı](luis-how-to-collaborate.md#azure-active-directory-tenant-user) . 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Uç Nokta

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Bir HTTP 403 hata durum kodu aldım. Bunu nasıl düzeltirim?

Fiyatlandırma katmanınız için saniye başına işlemleri veya aylık işlem sayısını aşarsanız 403 ve 429 hata durum kodları alırsınız. Fiyatlandırma katmanınızı artırın veya Language Understanding [kapsayıcıları](luis-container-howto.md)kullanın.

Tüm bu ücretsiz 1000 uç nokta sorgularını kullandığınızda veya fiyatlandırma katmanınızın aylık işlem kotasını aşarsanız, bir HTTP 403 hata durum kodu alırsınız. 

Bu hatayı onarmak için [fiyatlandırma katmanınızı](luis-how-to-azure-subscription.md#change-pricing-tier) daha yüksek bir katmana değiştirmeniz veya [Yeni bir kaynak oluşturup](get-started-portal-deploy-app.md#create-the-endpoint-resource) [uygulamanıza atamanız](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)gerekir.

Bu hata için çözümler şunlardır:

* [Azure Portal](https://portal.azure.com), Language Understanding kaynağınızın **kaynak yönetimi-> fiyatlandırma katmanında**, fiyatlandırma katmanınızı daha yüksek bir TPS katmanına değiştirin. Kaynağınız Language Understanding uygulamanıza zaten atanmışsa Language Understanding portalında herhangi bir şey yapmanız gerekmez.
*  Kullanımınız en yüksek fiyatlandırma katmanını aşarsa, önde gelen yük dengeleyiciye daha fazla Language Understanding kaynağı ekleyin. Kubernetes veya Docker Compose ile [Language Understanding kapsayıcısı](luis-container-howto.md) bu konuda yardımcı olabilir.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Bir HTTP 429 hata durum kodu aldım. Bunu nasıl düzeltirim?

Fiyatlandırma katmanınız için saniye başına işlemleri veya aylık işlem sayısını aşarsanız 403 ve 429 hata durum kodları alırsınız. Fiyatlandırma katmanınızı artırın veya Language Understanding [kapsayıcıları](luis-container-howto.md)kullanın.

Bu durum kodu, işlemleriniz fiyatlandırma katmanınızı aştığında döndürülür.  

Çözümler şunlardır:

* En yüksek katmanda değilseniz [fiyatlandırma katmanınızı artırabilirsiniz](luis-how-to-azure-subscription.md#change-pricing-tier).
* Kullanımınız en yüksek fiyatlandırma katmanını aşarsa, önde gelen yük dengeleyiciye daha fazla Language Understanding kaynağı ekleyin. Kubernetes veya Docker Compose ile [Language Understanding kapsayıcısı](luis-container-howto.md) bu konuda yardımcı olabilir.
* Bu durum kodunu aldığınızda, istemci uygulama isteklerinizi sizin uyguladığınız bir [yeniden deneme ilkesiyle](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) geçit olarak kullanabilirsiniz. 

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Uç nokta Sorgum beklenmeyen bir sonuç döndürdü. Ne yapmalıyım?

Beklenmeyen sorgu tahmin sonuçlarını yayımlanan model durumuna dayanır. Modeli düzeltmek için modeli değiştirmeniz, eğitme ve yeniden yayımlamanız gerekebilir. 

Model düzeltme ile başlayan [etkin olarak öğrenmeye](luis-how-to-review-endpoint-utterances.md).

Güncelleştirerek belirleyici eğitim kaldırabilirsiniz [uygulama sürümü ayarları API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) tüm eğitim verilerini kullanmak için.

Gözden geçirme [en iyi uygulamalar](luis-concept-best-practices.md) diğer ipuçları için. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Neden LUIS geçici bir çözüm veya sözcük ortasında sorguya alanları ekliyor mu?
LUIS [tokenizes](luis-glossary.md#token) utterance temel alarak [kültür](luis-language-support.md#tokenization). Parçalanmış değeri ve özgün değeri kullanılabilir [veri ayıklama](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Nasıl oluştururum ve uç noktası anahtarı bir LUIS atama?
[Uç nokta oluşturma](luis-how-to-azure-subscription.md) için azure'da, [hizmet](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) düzeyi. [Anahtarı](luis-how-to-azure-subscription.md) **[Azure kaynakları](luis-how-to-azure-subscription.md)** sayfasına atayın. Bu eyleme karşılık gelen hiçbir API yoktur. HTTP isteği için uç nokta için değiştirmeniz gerekir sonra [yeni uç nokta anahtarını kullanmak](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>LUIS puanları nasıl yorumlanacağı?
Sisteminizi, en yüksek Puanlama amaç değeri ne olursa olsun kullanmanız gerekir. Örneğin, 0,5 (daha az % 50'den) altında bir puan mutlaka LUIS düşük güven olduğunu gelmez. Daha fazla eğitim verileri yardımcı sağlama artırmak [puanı](luis-concept-prediction-score.md) olasılıkla hedefinin.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Benim uygulamamın Pano uç noktası isabet neden göremiyorum?
Uygulamanızın panosunda toplam uç noktası İsabeti düzenli olarak güncelleştirilir ancak Azure portalında LUIS uç nokta anahtarıyla ilişkili ölçümleri daha sık güncelleştirilir.

Panoda güncelleştirilmiş uç nokta isabetlerinizi görmüyorsanız, Azure portal oturum açın ve LUıN uç nokta anahtarınızla ilişkili kaynağı bulun ve **Toplam çağrı** ölçüsünü seçmek için **ölçümleri** açın. Uç nokta için birden fazla LUIS uygulaması kullandıysanız, Azure portalında ölçüm kullanan tüm LUIS uygulamalardan gelen çağrıları toplam sayısını gösterir.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Uç nokta kotasına yönelik bir PowerShell komutu var mı?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Uç nokta kotasını görmek için bir PowerShell komutu kullanabilirsiniz:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>LUIS uygulamamı dün çalıştığı ancak bugün 403 hataları alıyorum. Ben uygulama değişmedi. Bunu nasıl düzeltirim?
Bir LUSıS uç noktası anahtarı oluşturmak ve uygulamaya atamak için bu [yönergeleri](#how-do-i-create-and-assign-a-luis-endpoint-key) izleyin. Ardından, [Yeni uç nokta anahtarını kullanmak](luis-concept-keys.md)için ISTEMCI uygulamasının http isteğini uç noktaya değiştirmeniz gerekir. Farklı bir bölgede yeni bir kaynak oluşturduysanız, HTTP istemci isteği bölgesini de değiştirin.

### <a name="how-do-i-secure-my-luis-endpoint"></a>LUIS Noktam güvenliğini nasıl sağlayabilirim?
Bkz: [uç nokta güvenliği](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>LUIS sınırlar içinde çalışma

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Hedefleri ve LUIS uygulaması destekleyebileceği varlıkların sayısı nedir?
Bkz: [sınırları](luis-boundaries.md) başvuru.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Bir LUIS uygulaması amacı, en fazla sayısından daha oluşturmak istiyorsunuz. Ne yapmalıyım?

Bkz: [hedefleri için en iyi yöntemler](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>LUIS birden çok varlık sayısı ile bir uygulama oluşturmak istiyorsunuz. Ne yapmalıyım?

Bkz: [varlıklar için en iyi yöntemler](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Listeler sayısına ve tümcecik boyutunu sınırları nelerdir?
Uzunluğunun üst sınırı için bir [tümcecik listesi](./luis-concept-feature.md), bkz: [sınırları](luis-boundaries.md) başvuru.

### <a name="what-are-the-limits-on-example-utterances"></a>Örnek konuşma sınırları nelerdir?
Bkz: [sınırları](luis-boundaries.md) başvuru.

## <a name="testing-and-training"></a>Test ve eğitim

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Uygulamamı modellerinde bazıları için bölmesinde test toplu işi bazı hataları görüyorum. Bu sorunu gidermek ne?

Hataları, etiketlerinizi ve Modellerinizi ait tahminlerin arasında bazı bir tutarsızlık olduğunu gösterir. Sorunu gidermek için biri veya her ikisi şu görevleri yapın:
* LUIS amaçları arasında Ayrımcılığı geliştirmek amacıyla daha fazla etiket ekleyin.
* Hızlı bilgi LUIS yardımcı olmak için etki alanına özel sözlük tanıtan ifade listesi özellikleri ekleyin.

Bkz: [toplu test](luis-tutorial-batch-testing.md) öğretici.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Bir uygulamayı dışarı sonra (yeni bir uygulama kimliği ile) yeni bir uygulamaya yeniden içe tıkladığınızda LUIS tahmin puanları farklıdır. Bu neden gerçekleşir?

Bkz: [aynı uygulamanın bir kopyasını tahmin farklılıklardan](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Uygulamama yaptığım değişiklikleri sonra bazı konuşma yanlış ıntent'e gidin. Rastgele olarak kaybolması sorunu görünüyor. Bunu nasıl düzeltirim? 

Bkz: [tüm verilerle Train](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Uygulama yayımlama

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Kiracı kimliği "Anahtarı uygulamanıza ekleme" penceresinde nedir?
Azure'da, bir kiracı istemcisi veya bir hizmet ile ilişkili kuruluş temsil eder. Azure portalında Kiracı Kimliğinizi bulmak **dizin kimliği** kutusunu seçerek **Azure Active Directory** > **Yönet**  >  **Özellikleri**.

![Azure portalında Kiracı kimliği](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Neden atadığım daha uygulamama atanan daha fazla uç nokta anahtarları vardır?
Her LUIS uygulaması yazma başlangıç anahtarı kolaylık uç nokta listesinde yok. LUIS deneyebilirsiniz. Bu nedenle bu anahtar yalnızca birkaç uç noktası İsabeti sağlar.  

LUIS genel kullanıma (GA) şeklindeydi uygulamanız varsa, aboneliğinizdeki LUIS uç nokta anahtarları otomatik olarak atanır. Bu, GA geçiş kolaylaştırmak için yapıldı. Azure portalında yeni bir LUIS uç nokta anahtarlar _değil_ LUIS otomatik atanmış.

## <a name="key-management"></a>Anahtar yönetimi

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Nasıl yaparım? hangi anahtara ihtiyacım olduğunu ve bununla ne yapmalıyım? 

Yazma anahtarı ve tahmin çalışma zamanı anahtarı arasındaki farklar hakkında bilgi edinmek için bkz. [lusıs 'de yazma ve sorgu tahmini uç noktası anahtarları](luis-concept-keys.md) . 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Kotanın olmadığı hakkında bir hata aldım. Bunu nasıl düzeltirim? 

Daha fazla bilgi edinmek için bkz. HTTP durum kodu [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) ve [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Daha fazla uç nokta sorgusu işlemem gerekiyor. Nasıl yaparım? mi? 

Daha fazla bilgi edinmek için bkz. HTTP durum kodu [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) ve [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Bir yazma anahtarı oluşturdum, ancak bu, LUı portalında gösterilmiyor. Ne oldu?

Yazma [anahtarı deneyimine](luis-migration-authoring.md)geçtikten sonra, yazma anahtarları Luo portalında kullanılabilir.  

## <a name="app-management"></a>Uygulama yönetimi

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Kullanıcı konuşma günlüğünü nasıl indiririm?
Varsayılan olarak, kullanıcıların konuşma LUIS uygulamanızı günlüğe kaydeder. LUIS uygulamanızı kullanıcılara gönderme konuşma günlüğünü indirmek için Git **uygulamalarım**ve uygulamayı seçin. Bağlamsal araç çubuğunda, seçin **uç nokta günlükleri dışarı aktar**. Günlük bir virgülle ayrılmış değer (CSV) dosyası olarak biçimlendirilir.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Konuşma günlüğe kaydetmeyi nasıl devre dışı bırakabilirim?
Kullanıcı konuşma oturumdan ayarlayarak etkinleştirebilirsiniz `log=false` içinde sorgu LUIS istemci uygulamanızın kullandığı uç nokta URL'si. Ancak, konuşma önerebilir veya temel alan performansı LUIS uygulamanızın olanağı oturumdan kapatma devre dışı bırakır [etkin olarak öğrenmeye](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Ayarlarsanız `log=false` veri gizliliği kaygıları nedeniyle, bu kullanıcı konuşma kaydını LUIS ' indirin veya uygulamanızı geliştirmek için bu konuşma kullanın.

Günlüğe kaydetme, konuşma, yalnızca depolama alanıdır.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Neden oturum my uç nokta konuşma istemez miyiz?
Tahmin analizi için günlüğünüzü kullanıyorsanız, test konuşma oturum yakalamayın.

## <a name="data-management"></a>Veri yönetimi

### <a name="can-i-delete-data-from-luis"></a>LUIS verileri silebilir miyim?

* LUIS eğitim için kullanılan örnek konuşma her zaman silebilirsiniz. LUIS uygulamanızdan bir örnek utterance silerseniz, LUIS web hizmetinden kaldırılır ve dışarı aktarma için kullanılamaz.
* Konuşma içinde LUIS önerir kullanıcı konuşma listesinden silebilirsiniz **gözden geçirin, konuşma uç noktası** sayfası. Konuşma bu listeden silme önerilmesini engelliyor, ancak bunları günlüklerinden silmez.
* Bir hesabı silerseniz, tüm uygulamalar, kendi örnek konuşma ve günlükleri birlikte silinir. Veriler kalıcı olarak silinmeden önce 60 gün boyunca sunucularda tutulur.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Microsoft, LUIS için gönderebilirim verileri nasıl yönetir?

[Güven Merkezi](https://www.microsoft.com/trustcenter) sunduğumuz ve veri yönetimi ve erişimi Azure Hizmetleri için seçenekleri açıklar.

## <a name="language-and-translation-support"></a>Dil ve çeviri desteği

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Tek bir dilde bir uygulamaya sahip ve başka bir dilde paralel bir uygulama oluşturmak istiyorsanız bildirimi. Bunu yapmanın en kolay yolu nedir?
1. Uygulamanızı dışarı aktarın.
2. Hedef dil için dışarı aktarılan uygulama JSON dosyasındaki etiketli sesleri çevirin.
3. Amaç ve varlıkları adlarını değiştirme veya oldukları gibi bunları bırakın gerekebilir.
4. Son olarak, hedef dilde bir LUIS uygulaması için uygulama içeri aktarın.

## <a name="app-notification"></a>Uygulama bildirimi

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Neden neredeyse kotası aşıldı ben bildiren bir e-posta almak?
Yazma başlangıç anahtarınızı yalnızca 1000 kullanılabilir uç nokta, bir ay sorgular. Bir LUIS uç noktası anahtarı (ücretsiz veya Ücretli) oluşturun ve uç nokta sorgu oluştururken bu anahtarı kullanın. Uç nokta sorguları bir bot veya başka bir istemci uygulaması oluşturuyorsanız, LUIS uç noktası anahtarı var. değiştirmeniz gerekir.

## <a name="bots"></a>Botlar

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>LUMY bot çalışmıyor. Ne yapmalıyım?

İlk sorun, sorunun luya BASIS ara yazılımı dışında gerçekleşmesidir. 

#### <a name="resolve-issue-in-luis"></a>LUSıS 'de sorunu çözme
[Luo uç noktasındaki](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)Luo 'ya aynı söylenişi geçirin. Bir hata alırsanız, hata artık döndürülünceye kadar lu, sorununu çözün. Sık karşılaşılan hatalar şunlardır:

* `Out of call volume quota. Quota will be replenished in <time>.`-bu sorun, yazma anahtarından bir [uç nokta anahtarına](luis-how-to-azure-subscription.md) değiştirmeniz gerektiğini ya da [Hizmet katmanlarını](luis-how-to-azure-subscription.md#change-pricing-tier)değiştirmeniz gerektiğini gösterir. 

#### <a name="resolve-issue-in-azure-bot-service"></a>Azure bot hizmetinde sorunu çözme

Azure bot hizmetini kullanıyorsanız ve sorun **Web sohbeti Içindeki testin** `Sorry, my bot code is having an issue`döndürmesi durumunda, günlüklerinizi kontrol edin:

1. Azure portal, bot için, **bot yönetimi** bölümünde **Oluştur**' u seçin.
1. Çevrimiçi kod düzenleyicisini açın. 
1. Üstteki, mavi gezinti çubuğunda, bot adını (sağdaki ikinci öğe) seçin.
1. Sonuç açılan listesinde, **kudu konsolunu aç**' ı seçin.
1. **Günlük dosyaları**' nı ve ardından **uygulama**' yı seçin. Tüm günlük dosyalarını gözden geçirin. Uygulama klasöründe hatayı görmüyorsanız, **LogFiles**altındaki tüm günlük dosyalarını gözden geçirin. 
1. Gibi derlenmiş bir dil kullanıyorsanız projenizi yeniden oluşturmayı unutmayın C#.

> [!Tip] 
> Konsol paketleri de yükleyebilir. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Bot Framework ile yerel makinede hata ayıklama sırasında sorunu çözün. 

Bir bot 'ın yerel hata ayıklaması hakkında daha fazla bilgi edinmek için bkz. [bir bot hata ayıklama](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>LUIS tümleştirme

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>LUIS uygulamamı Azure web app botu abonelik işlemi sırasında oluşturulduğu?
Bir LUIS şablonu seçin ve seçin, **seçin** düğmesi Şablon bölmesinde, sol taraftaki bölmede şablon türü içerecek şekilde değiştirir ve LUIS şablonu oluşturmak için hangi bölgede sorar. Web app botu işlemi yine de bir LUIS abonelik oluşturmaz.

![LUIS şablonu web app botu bölgesi](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Hangi LUIS bölgeleri Bot Framework konuşma Hazırlama işlemi destekler?
[Konuşma Hazırlama işlemi](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) yalnızca merkezi (ABD) örneğinde LUIS uygulamalar için desteklenir.

## <a name="api-programming-strategies"></a>API programlama stratejileri

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Nasıl yaparım? bir kaynağın LUO bölgesini programlı olarak alır mi? 

Veya Node. js kullanarak C# bölgeyi programlı bir şekilde [bulmak](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) için Luo örneğini kullanın. 

## <a name="luis-service"></a>LUIS hizmeti

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Language Understanding (LUIS) şirket içi kullanılabilir mi ya da özel bulutta?

Evet, LUIS kullanabileceğiniz [kapsayıcı](luis-container-howto.md) kullanımını ölçmek için gerekli bağlantı varsa, bu senaryolar için. 

## <a name="migrating-to-the-next-version"></a>Sonraki sürüme geçme

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Nasıl yaparım? v3 API 'sine geçiş yapılsın mı? 

[Lusıs uygulamaları Için API v2 'yi v3 geçiş kılavuzuna](luis-migration-api-v3.md) bakın

## <a name="build-2019-conference-announcements"></a>Derleme 2019 Konferans Duyuruları

Aşağıdaki özellikler Build 2019 Konferansı 'nda yayımlanmıştır:

* [V3 API geçiş kılavuzu önizlemesi](luis-migration-api-v3.md)
* [Geliştirilmiş analiz panosu](luis-how-to-use-dashboard.md)
* [Geliştirilmiş önceden oluşturulmuş etki alanları](luis-reference-prebuilt-domains.md) 
* [Dinamik liste varlıkları](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Dış varlıklar](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videolar:

* [Azure konuşma AI kullanarak işinizi bir sonraki nesil için ölçeklendirin](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Sonraki adımlar

LUIS hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:
* [LUIS ile etiketlenmiş bir yığın taşması soru](https://stackoverflow.com/questions/tagged/luis)
* [Hizmetleri MSDN dil akıllı anlama (LUIS) Forumu](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)

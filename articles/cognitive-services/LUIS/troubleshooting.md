---
title: Sık sorulan sorular (SSS)-LUSıS
description: Bu makale Language Understanding (LUO) hakkında sık sorulan soruların yanıtlarını içerir.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.openlocfilehash: 97b7c02a418a87a0700414e19bc939bda899d073
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503839"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding ile ilgili Sık Sorulan Sorular (SSS)

Bu makale Language Understanding (LUO) hakkında sık sorulan soruların yanıtlarını içerir.

## <a name="whats-new"></a>Yenilikler

Language Understanding (LUSıS) yenilikleri hakkında [daha fazla bilgi edinin](whats-new.md) .

<a name="luis-authoring"></a>

## <a name="authoring"></a>Yazma

### <a name="what-are-the-luis-best-practices"></a>Lune en iyi uygulamalar nelerdir?
[Yazma döngüsünü](luis-concept-app-iteration.md)başlatın ve [en iyi uygulamaları](luis-concept-best-practices.md)okuyun.

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>LUSıS 'de Uygulamam oluşturmaya başlayabilmenin en iyi yolu nedir?

Uygulamanızı oluşturmanın en iyi yolu, [artımlı bir işlemdir](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Uygulamamın amaçlarını modellemek için iyi bir uygulama nedir? Daha belirgin veya daha fazla genel amaç oluşturmalıyım?

Genellikle örtüşme kadar genel olmayan amaçlar ' ı seçin, ancak bu nedenle, LUL 'nın benzer amaçlar arasında ayrım yapması zor olur. Ayrılmış özel amaçlar oluşturmak, LUSıS modellemesi için en iyi uygulamalardan biridir.

### <a name="is-it-important-to-train-the-none-intent"></a>Hiçbiri amacını eğmamak önemlidir mi?

Evet, diğer amaçlar için daha fazla etiket eklerken, **hiçbiri** amacınızı daha fazla şekilde eğitmeniz yararlı olur. Bir amaca eklenen her 10 **etiket için,** iyi bir oran 1 veya 2 etikettir. Bu oran, LUO 'nun ayrılmış gücünü artırır.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Yazıdıklarla yazım hatalarını nasıl düzeltebilirim?

Bkz. [Bing yazım denetimi API'si v7](luis-tutorial-bing-spellcheck.md) öğreticisi. Lua, Bing Yazım Denetimi API'si v7 tarafından uygulanan limitleri zorlar.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>LUMY uygulamamı programlı bir şekilde Düzenle Nasıl yaparım??
LUSıS uygulamanızı programlı bir şekilde düzenlemek için [yazma API](https://go.microsoft.com/fwlink/?linkid=2092087)'sini kullanın. Yazma API 'sini çağırma örnekleri için bkz. Node.jskullanarak [lusıs yazma API 'Sini çağırma](./get-started-get-model-rest-apis.md) ve [bir Lusıs uygulaması oluşturma ](./luis-tutorial-node-import-utterances-csv.md) . Yazma API 'SI, bir uç nokta anahtarı yerine bir [yazma anahtarı](luis-how-to-azure-subscription.md#azure-resources-for-luis) kullanmanızı gerektirir. Programlı yazma, ayda 1.000.000 çağrının ve saniyede beş işlem sağlar. LUO ile kullandığınız anahtarlar hakkında daha fazla bilgi için bkz. [anahtarları yönetme](./luis-how-to-azure-subscription.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Normal ifade eşleşen bir model özelliği nerede?
Önceki **desen özelliği** Şu anda kullanım dışıdır ve **[desenlerle](luis-concept-patterns.md)** değiştirilmiştir.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Nasıl yaparım? doğru verileri çekmek için bir varlık mi kullanıyorsunuz?
Bkz. [varlıklar](luis-concept-entity-types.md) ve [veri ayıklama](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Bir örnek noktalama, noktalama işaretleri mi içeriyor?
Aşağıdaki çözümlerden birini kullanın:
* [Noktalama işaretlerini](luis-reference-application-settings.md#punctuation-normalization) yoksay
* Amaç için farklı Çeşitlemeler örnek olarak ekleyin
* Noktalama işaretlerini [yoksaymak için, söz dizimi](luis-concept-patterns.md#pattern-syntax) ile birlikte bir örnek stili ekleyin.

### <a name="does-luis-currently-support-cortana"></a>LUŞU anda Cortana 'Yı destekliyor mu?

Cortana önceden oluşturulmuş uygulamalar 2017 ' de kullanımdan kaldırılmıştır. Artık desteklenmiyordur.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Bir LUO uygulamasının sahipliğini Nasıl yaparım? aktarmak istiyor musunuz?
Bir LUO uygulamasını farklı bir Azure aboneliğine aktarmak için, Luo uygulamasını dışarı aktarın ve yeni bir hesap kullanarak içeri aktarın. Bu uygulamayı çağıran istemci uygulamasındaki LUSıS uygulama KIMLIĞINI güncelleştirin. Yeni uygulama, özgün uygulamadan biraz farklı bir LUO puanı döndürebilir.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Önceden oluşturulmuş bir varlık özel varlığım yerine örnek bir şekilde etiketlidir. Bu Nasıl yaparım? düzeltilsin mi?

Luu portalında, ayıklama işlemi sırasında ilgilendiğiniz tam varlığın metnini etiketleyebilir. Lua portalı doğru varlık tahminini göstermiyorsa, daha fazla işlem eklemeniz ve varlığı metin içinde etiketleyip bir özellik eklemeniz gerekebilir.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Bir uygulama veya sürüm dosyasını içeri aktarmaya çalıştım, ancak bir hata oluştu, ne oldu?

[Sürüm içeri aktarma hataları](luis-how-to-manage-versions.md#import-errors)hakkında daha fazla bilgi edinin.

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>İşbirliği ve katkıda bulunma

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Nasıl yaparım?, Azure Active Directory (Azure AD) veya Azure rol tabanlı erişim denetimi (Azure RBAC) ile BASıS erişimi sağlar mi?

Ortak çalışanlarla erişim verme hakkında bilgi edinmek için bkz. [Azure Active Directory kaynakları](luis-how-to-collaborate.md#azure-active-directory-resources)  ve [Azure Active Directory Kiracı Kullanıcı](luis-how-to-collaborate.md#azure-active-directory-tenant-user) .

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Uç Nokta

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Bir HTTP 403 hata durum kodu aldım. Nasıl düzeltebilirim?

Fiyatlandırma katmanınız için saniye başına işlemleri veya aylık işlem sayısını aşarsanız 403 ve 429 hata durum kodları alırsınız. Fiyatlandırma katmanınızı artırın veya Language Understanding [kapsayıcıları](luis-container-howto.md)kullanın.

Tüm bu ücretsiz 1000 uç nokta sorgularını kullandığınızda veya fiyatlandırma katmanınızın aylık işlem kotasını aşarsanız, bir HTTP 403 hata durum kodu alırsınız.

Bu hatayı onarmak için [fiyatlandırma katmanınızı](luis-how-to-azure-subscription.md#change-the-pricing-tier) daha yüksek bir katmana değiştirmeniz veya [Yeni bir kaynak oluşturup](get-started-portal-deploy-app.md#create-the-endpoint-resource) [uygulamanıza atamanız](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)gerekir.

Bu hata için çözümler şunlardır:

* [Azure Portal](https://portal.azure.com), Language Understanding kaynağınızın **kaynak yönetimi-> fiyatlandırma katmanında**, fiyatlandırma katmanınızı daha yüksek bir TPS katmanına değiştirin. Kaynağınız Language Understanding uygulamanıza zaten atanmışsa Language Understanding portalında herhangi bir şey yapmanız gerekmez.
*  Kullanımınız en yüksek fiyatlandırma katmanını aşarsa, önde gelen yük dengeleyiciye daha fazla Language Understanding kaynağı ekleyin. Kubernetes veya Docker Compose ile [Language Understanding kapsayıcısı](luis-container-howto.md) bu konuda yardımcı olabilir.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Bir HTTP 429 hata durum kodu aldım. Nasıl düzeltebilirim?

Fiyatlandırma katmanınız için saniye başına işlemleri veya aylık işlem sayısını aşarsanız 403 ve 429 hata durum kodları alırsınız. Fiyatlandırma katmanınızı artırın veya Language Understanding [kapsayıcıları](luis-container-howto.md)kullanın.

Bu durum kodu, işlemleriniz fiyatlandırma katmanınızı aştığında döndürülür.

Çözümler şunlardır:

* En yüksek katmanda değilseniz [fiyatlandırma katmanınızı artırabilirsiniz](luis-how-to-azure-subscription.md#change-the-pricing-tier).
* Kullanımınız en yüksek fiyatlandırma katmanını aşarsa, önde gelen yük dengeleyiciye daha fazla Language Understanding kaynağı ekleyin. Kubernetes veya Docker Compose ile [Language Understanding kapsayıcısı](luis-container-howto.md) bu konuda yardımcı olabilir.
* Bu durum kodunu aldığınızda, istemci uygulama isteklerinizi sizin uyguladığınız bir [yeniden deneme ilkesiyle](/azure/architecture/best-practices/transient-faults#general-guidelines) geçit olarak kullanabilirsiniz.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Uç nokta sorgum beklenmeyen sonuçlar döndürdü. Ne yapmalıyım?

Beklenmeyen sorgu tahmin sonuçları, yayımlanan modelin durumuna göre yapılır. Modeli düzeltmek için modeli değiştirmeniz, eğitme ve yeniden yayımlamanız gerekebilir.

Modelin düzeltilmesi, [etkin öğrenme](luis-how-to-review-endpoint-utterances.md)ile başlar.

Tüm eğitim verilerini kullanmak için [uygulama sürümü ayarları API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) 'sini güncelleştirerek belirleyici olmayan eğitimi kaldırabilirsiniz.

Diğer ipuçları için [en iyi uygulamaları](luis-concept-best-practices.md) gözden geçirin.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Lune neden, sözcüklerin ortasında veya içinde sorguya boşluk ekler?
LUSıS, [kültürü](luis-language-support.md#tokenization)temel alan bir şekilde [simgeleştirir](luis-glossary.md#token) . Hem özgün değer hem de simgeleştirilmiş değer [veri ayıklama](luis-concept-data-extraction.md#tokenized-entity-returned)için kullanılabilir.

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Nasıl yaparım? bir LUSıS uç noktası anahtarı oluşturup atamayı mı istiyorsunuz?
Azure 'da [hizmet](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) düzeyiniz için [uç nokta anahtarı oluşturun](luis-how-to-azure-subscription.md) . [Anahtarı](luis-how-to-azure-subscription.md) **[Azure kaynakları](luis-how-to-azure-subscription.md)** sayfasına atayın. Bu eylem için karşılık gelen bir API yok. Ardından, [Yeni uç nokta anahtarını kullanmak](luis-how-to-azure-subscription.md)için http isteğini uç noktaya değiştirmeniz gerekir.

### <a name="how-do-i-interpret-luis-scores"></a>LUSıS puanlarını Nasıl yaparım? yorumlanması mı?
Sisteminizin değeri ne olursa olsun, en yüksek Puanlama amacını kullanması gerekir. Örneğin, 0,5 altına bir puan (%50 ' den az) , LUıN düşük güvenilirliğe sahip olduğu anlamına gelmez. Daha fazla eğitim verisi sağlamak, en olası amacın [Puanını](luis-concept-prediction-score.md) artırmaya yardımcı olabilir.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Neden uç nokta isabetlerimi uygulamamın panosundan görmüyorum?
Uygulamanızın panosundaki toplam bitiş noktası isabetlerinin düzenli aralıklarla güncelleştirildiği, ancak Azure portal BASıS uç nokta anahtarınızla ilişkili ölçümler daha sık güncelleştirilir.

Panoda güncelleştirilmiş uç nokta isabetlerinizi görmüyorsanız, Azure portal oturum açın ve LUıN uç nokta anahtarınızla ilişkili kaynağı bulun ve **Toplam çağrı** ölçüsünü seçmek için **ölçümleri** açın. Uç nokta anahtarı birden fazla LUO uygulaması için kullanılırsa, Azure portal ölçümü, kendisini kullanan tüm LUYA uygulamalarının Toplam çağrı sayısını gösterir.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Uç nokta kotasına yönelik bir PowerShell komutu var mı?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Uç nokta kotasını görmek için bir PowerShell komutu kullanabilirsiniz:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>LUMY Uygulamam dün çalışıyor, ancak bugün 403 hata alıyorum. Uygulamayı değiştirdim. Nasıl düzeltebilirim?
Bir LUSıS uç noktası anahtarı oluşturmak ve uygulamaya atamak için bu [yönergeleri](#how-do-i-create-and-assign-a-luis-endpoint-key) izleyin. Ardından, [Yeni uç nokta anahtarını kullanmak](luis-how-to-azure-subscription.md)için ISTEMCI uygulamasının http isteğini uç noktaya değiştirmeniz gerekir. Farklı bir bölgede yeni bir kaynak oluşturduysanız, HTTP istemci isteği bölgesini de değiştirin.

### <a name="how-do-i-secure-my-luis-endpoint"></a>LUMY uç noktanmın güvenli Nasıl yaparım? mi?
Bkz. [uç noktayı güvenli hale getirme](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>LUSıS sınırları içinde çalışma

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Bir LUIN uygulamasının destekleyebileceği en fazla amaç ve varlık sayısı nedir?
[Sınır](luis-limits.md) başvurusuna bakın.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>En fazla amaç sayısından daha fazla olan bir LUO uygulaması oluşturmak istiyorum. Ne yapmalıyım?

[Amaçlar Için en iyi uygulamalar](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)bölümüne bakın.

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Tümcecik listelerinin sayısı ve boyutu sınırları nelerdir?
[Tümcecik listesinin](./luis-concept-feature.md)uzunluk üst sınırı için bkz. [sınır](luis-limits.md) başvurusu.

### <a name="what-are-the-limits-on-example-utterances"></a>Örnek dıklarıyla ilgili sınırlamalar nelerdir?
[Sınır](luis-limits.md) başvurusuna bakın.

## <a name="testing-and-training"></a>Test ve eğitim

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Uygulamamda bazı modeller için Batch test bölmesinde bazı hatalar görüyorum. Bu sorunu nasıl ele alabilirim?

Hatalar, etiketlerinizin ve modellerinizin tahminlerinin arasında bir tutarsızlık olduğunu gösterir. Sorunu gidermek için aşağıdaki görevlerden birini veya her ikisini birden yapın:
* LUTO 'ıN amaçlar arasında ayırt edici şekilde iyileştirilmesine yardımcı olmak için daha fazla etiket ekleyin.
* LUTO 'NıN daha hızlı öğrenmesi için, etki alanına özgü sözlük sağlayan tümcecik listesi özellikleri ekleyin.

Bkz. [Batch test](./luis-how-to-batch-test.md) öğreticisi.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Bir uygulama verildiğinde yeni bir uygulamaya yeniden aktarıldığında (yeni bir uygulama KIMLIĞI ile), LUO tahmin puanları farklıdır. Bunun nedeni nedir?

[Aynı uygulamanın kopyaları arasındaki tahmin farklarını](luis-concept-prediction-score.md#review-intents-with-similar-scores)inceleyin.

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Uygulamamda değişiklik yaptıktan sonra bazı söyler yanlış amaca gider. Sorun rastgele göründüyor gibi görünüyor. Nasıl düzeltebilirim?

Bkz. [tüm verilerle eğitme](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Uygulama yayımlama

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>"Uygulamanıza anahtar ekleme" penceresinde kiracı KIMLIĞI nedir?
Azure 'da kiracı, bir hizmetle ilişkili istemciyi veya kuruluşu temsil eder. Azure Portal, **dizin kimliği** kutusundaki **Azure Active Directory**  >    >  **özellikleri** Yönet ' i seçerek kiracı kimliğinizi bulun.

![Azure portal Kiracı KIMLIĞI](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Uygulamamda atandığım kadar neden daha fazla uç nokta anahtarı var?
Her bir LUIN uygulaması, uç nokta listesindeki yazma/başlangıç anahtarına kolaylık olarak sahiptir. Bu anahtar, LUSıS 'yi deneyebilmeniz için yalnızca birkaç uç nokta isabetlerine izin verir.

Eğer uygulamanız, Lu, genel kullanıma hazır olmadan (GA) varsa, aboneliğinizdeki LUSıS uç nokta anahtarları otomatik olarak atanır. Bu, GA geçişini kolaylaştırmak için gerçekleştirildi. Azure portal tüm yeni Luo uç nokta anahtarları LUO _'ya otomatik olarak_ atanmaz.

## <a name="key-management"></a>Anahtar yönetimi

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Nasıl yaparım? hangi anahtara ihtiyacım olduğunu ve bununla ne yapmalıyım?

Yazma anahtarı ve tahmin çalışma zamanı anahtarı arasındaki farklar hakkında bilgi edinmek için bkz. [lusıs 'de yazma ve sorgu tahmini uç noktası anahtarları](luis-how-to-azure-subscription.md) .

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Kotanın olmadığı hakkında bir hata aldım. Nasıl düzeltebilirim?

Daha fazla bilgi edinmek için bkz. HTTP durum kodu [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) ve [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Daha fazla uç nokta sorgusu işlemem gerekiyor. Bunu nasıl yapabilirim?

Daha fazla bilgi edinmek için bkz. HTTP durum kodu [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) ve [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) .

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Bir yazma anahtarı oluşturdum, ancak bu, LUı portalında gösterilmiyor. Neler oldu?

Yazma [anahtarı deneyimine](luis-migration-authoring.md)geçtikten sonra, yazma anahtarları Luo portalında kullanılabilir.

## <a name="app-management"></a>Uygulama yönetimi

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Kullanıcı Nasıl yaparım? bir günlüğü indirmek mi istiyorsunuz?
Varsayılan olarak, LUSıS uygulamanız kullanıcılardan gelen günlükleri günlüğe kaydeder. Kullanıcıların LUSıS uygulamanıza gönderdikleri bir günlük günlüğünü indirmek için **uygulamalarım**' a gidin ve uygulamayı seçin. Bağlam araç çubuğunda, **uç nokta günlüklerini dışarı aktar**' ı seçin. Günlük, virgülle ayrılmış değer (CSV) dosyası olarak biçimlendirilir.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Utterslar günlüğünü nasıl devre dışı bırakabilirim?
`log=false`İstemci UYGULAMANıZıN LUL 'yi sorgulamak için kullandığı uç nokta URL 'si ayarını yaparak Kullanıcı çeşidlerini günlüğe kaydetmeyi devre dışı bırakabilirsiniz. Ancak, günlüğü kapatmak, LUL uygulamanızın, [etkin öğrenmeyi](luis-concept-review-endpoint-utterances.md#what-is-active-learning)temel alan, bir yandan gelen performansı ve performansı iyileştirebilme yeteneğini devre dışı bırakır. `log=false`Veri gizliliği sorunları nedeniyle ayarlarsanız, bu kullanıcı aradıklarından bir KAYDıNı lusıs 'den indiremez veya uygulamanızı geliştirmek için bu söyleyenler kullanabilirsiniz.

Günlüğe kaydetme, tek tek depolamadaki depodır.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Neden tüm uç nokta dıklarımı günlüğe kaydedilmesini istemiyorum?
Günlüğü tahmin analizi için kullanıyorsanız, günlükteki test araslarını yakalamaz.

## <a name="data-management"></a>Veri yönetimi

### <a name="can-i-delete-data-from-luis"></a>LUSıS 'den verileri silebilir miyim?

* Her zaman, LUFOR eğitimi için kullanılan örnek utbotları silebilirsiniz. LUSıS uygulamanızın bir örneğini silerseniz, bu, LUSıS Web hizmetinden kaldırılır ve dışarı aktarma için kullanılamaz.
* Konuşma **uç noktası sıralayıcısı** SAYFASıNDA, luya 'nın önerdiği Kullanıcı arasları listesinden gelen noktaları silebilirsiniz. Bu listedeki söyleymeleri silmek, bunların önerilmesine izin vermez, ancak bunları günlüklerden silmez.
* Bir hesabı silerseniz, tüm uygulamalar, örnek ve günlükleriyle birlikte silinir. Veriler kalıcı olarak silinmeden önce 60 gün boyunca sunucularda tutulur.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Microsoft 'un LUYA gönderdiğim verileri nasıl yöneteceği?

[Güven Merkezi](https://www.microsoft.com/trustcenter) taahhütlerini ve Azure hizmetlerinde veri yönetimi ve erişim seçeneklerinizi açıklar.

## <a name="language-and-translation-support"></a>Dil ve çeviri desteği

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Tek dilde bir uygulamam var ve başka bir dilde paralel uygulama oluşturmak istiyorum. Bunu yapmanın en kolay yolu nedir?
1. Uygulamanızı dışarı aktarın.
2. Verilen uygulamanın JSON dosyasındaki etiketlenmiş utsliği hedef dile çevirin.
3. Amaç ve varlıkların adlarını değiştirmeniz veya bunları olduğu gibi bırakmanız gerekebilir.
4. Son olarak, uygulamayı hedef dilde bir LUSıS uygulamasına sahip olacak şekilde içeri aktarın.

## <a name="app-notification"></a>Uygulama bildirimi

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Neredeyse kotanın ne olduğunu belirten bir e-posta aldım?
Yazma/başlangıç anahtarınız yalnızca bir ay 1000 uç nokta sorgusuna izin verilir. Bir LUSıS uç noktası anahtarı (ücretsiz veya ücretli) oluşturun ve uç nokta sorguları yaparken bu anahtarı kullanın. Bir bot veya başka bir istemci uygulamasından uç nokta sorguları yapıyorsanız, bu uç nokta anahtarını orada değiştirmeniz gerekir.

## <a name="bots"></a>Botlar

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>LUMY bot çalışmıyor. Ne yapmalıyım?

İlk sorun, sorunun luya BASIS ara yazılımı dışında gerçekleşmesidir.

#### <a name="resolve-issue-in-luis"></a>LUSıS 'de sorunu çözme
[Luo uç noktasındaki](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)Luo 'ya aynı söylenişi geçirin. Bir hata alırsanız, hata artık döndürülünceye kadar lu, sorununu çözün. Yaygın hatalar şunlardır:

* `Out of call volume quota. Quota will be replenished in <time>.` -Bu sorun, yazma anahtarından bir [uç nokta anahtarına](luis-how-to-azure-subscription.md) değiştirmeniz gerektiğini veya [Hizmet katmanlarını](luis-how-to-azure-subscription.md#change-the-pricing-tier)değiştirmeniz gerektiğini gösterir.

#### <a name="resolve-issue-in-azure-bot-service"></a>Azure bot hizmetinde sorunu çözme

Azure bot hizmetini kullanıyorsanız ve sorun **Web sohbeti Içindeki testin** dönüşse, `Sorry, my bot code is having an issue` günlüklerinizi kontrol edin:

1. Azure portal, bot için, **bot yönetimi** bölümünde **Oluştur**' u seçin.
1. Çevrimiçi kod düzenleyicisini açın.
1. Üstteki, mavi gezinti çubuğunda, bot adını (sağdaki ikinci öğe) seçin.
1. Sonuç açılan listesinde, **kudu konsolunu aç**' ı seçin.
1. **Günlük dosyaları**' nı ve ardından **uygulama**' yı seçin. Tüm günlük dosyalarını gözden geçirin. Uygulama klasöründe hatayı görmüyorsanız, **LogFiles** altındaki tüm günlük dosyalarını gözden geçirin.
1. C# gibi derlenmiş bir dil kullanıyorsanız projenizi yeniden oluşturmayı unutmayın.

> [!Tip]
> Konsol paketleri de yükleyebilir.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Bot Framework ile yerel makinede hata ayıklama sırasında sorunu çözün.

Bir bot 'ın yerel hata ayıklaması hakkında daha fazla bilgi edinmek için bkz. [bir bot hata ayıklama](/azure/bot-service/bot-service-debug-bot).

## <a name="integrating-luis"></a>LUSıS 'yi tümleştirme

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Azure Web App bot abonelik işlemi sırasında LUMY Uygulamam nerede oluşturulur?
Bir LUSıS şablonu seçer ve şablon bölmesinde **Seç** düğmesini seçerseniz, sol taraftaki bölme şablon türünü dahil etmek için değişir ve Lune 'nın bu şablonu oluşturmak için bölgeyi sorar. Web uygulaması bot işlemi, ancak bir LUSıS aboneliği oluşturmaz.

![LUSıS şablonu Web uygulaması bot bölgesi](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>BOTıN bölgeleri, bot Framework konuşma primi destekler?
[Konuşma primi](/bot-framework/bot-service-manage-speech-priming) yalnızca orta (US) örneğindeki lusıs uygulamaları için desteklenir.

## <a name="api-programming-strategies"></a>API programlama stratejileri

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Nasıl yaparım? bir kaynağın LUO bölgesini programlı olarak alır mi?

C# veya Node.Js kullanarak bölgeyi programlı bir şekilde [bulmak](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) için Luo örneğini kullanın.

## <a name="luis-service"></a>LUIS hizmeti

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Language Understanding (LUSıS) şirket içinde veya özel bulutta kullanılabilir mi?

Evet, kullanım ölçümü için gerekli bağlantınız varsa, bu senaryolar için LUO [kapsayıcısını](luis-container-howto.md) kullanabilirsiniz.

## <a name="migrating-to-the-next-version"></a>Sonraki sürüme geçme

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Nasıl yaparım? v3 API 'sine geçiş yapılsın mı?

[Lusıs uygulamaları Için API v2 'yi v3 geçiş kılavuzuna](luis-migration-api-v3.md) bakın

## <a name="build-2019-conference-announcements"></a>Derleme 2019 Konferans Duyuruları

Aşağıdaki özellikler Build 2019 Konferansı 'nda yayımlanmıştır:

* [V3 API geçiş kılavuzu önizlemesi](luis-migration-api-v3.md)
* [Geliştirilmiş analiz panosu](luis-how-to-use-dashboard.md)
* [Geliştirilmiş önceden oluşturulmuş etki alanları](luis-reference-prebuilt-domains.md)
* [Dinamik liste varlıkları](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Dış varlıklar](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videolar:

* [Azure konuşma AI kullanarak işinizi bir sonraki nesil için ölçeklendirin](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Sonraki adımlar

LUO hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:
* [LUSıS ile etiketlenmiş Stack Overflow soruları](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&MSDN Language Understanding Intelligent Services (LUSıS) için soru sayfası](/answers/topics/azure-language-understanding.html)
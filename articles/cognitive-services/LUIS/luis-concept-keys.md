---
title: LUSıS ile yazma ve çalışma zamanı anahtarlarını kullanma
titleSuffix: Azure Cognitive Services
description: LUSıS iki anahtar kullanır, modelinizi oluşturmak için yazma anahtarı ve tahmin uç noktasını Kullanıcı utsları ile sorgulamak için çalışma zamanı anahtarı kullanılır.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b58aa97dbb97bade87a38456c58df8f93a29946f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901695"
---
# <a name="authoring-and-runtime-keys"></a>Yazma ve çalışma zamanı anahtarları

Language Understanding (LUA) iki hizmet ve API kümesine sahiptir: 

* Yazma (daha önce _programlı_olarak biliniyordu)
* Tahmin çalışma zamanı

Birlikte çalışmak istediğiniz hizmete ve bununla nasıl çalışmak istediğinize bağlı olarak birkaç anahtar türü vardır.

## <a name="non-azure-resources-for-luis"></a>LUSıS için Azure dışı kaynaklar

### <a name="starter-key"></a>Başlangıç anahtarı

LUSıS kullanmaya ilk kez başladığınızda, sizin için bir **Başlangıç anahtarı** oluşturulur. Bu kaynak şunları sağlar:

* LUıS portalı veya API 'Ler (SDK 'Lar dahil) aracılığıyla ücretsiz yazma hizmeti istekleri
* bir tarayıcı, API veya SDK aracılığıyla ayda ücretsiz 1.000 tahmin uç noktası isteği

## <a name="azure-resources-for-luis"></a>LUSıS için Azure kaynakları

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUSıS üç tür Azure kaynağına izin verir: 
 
|Anahtar|Amaç|Bilişsel hizmet `kind`|Bilişsel hizmet `type`|
|--|--|--|--|
|[Yazma anahtarı](#programmatic-key)|Yazma, eğitim, yayımlama ve test etme ile uygulama verilerine erişin ve bunları yönetin. LUSıS uygulamalarını programlı olarak yazmak istiyorsanız bir LUSıS yazma anahtarı oluşturun.<br><br>`LUIS.Authoring` anahtarının amacı şunları yapmanıza izin versağlamaktır:<br>* Eğitim ve yayımlama dahil Language Understanding uygulamaları ve modelleri program aracılığıyla yönetme<br> * [katkıda bulunan rolüne](#contributions-from-other-authors)kişi atayarak yazma kaynağına yönelik izinleri denetleyin.|`LUIS.Authoring`|`Cognitive Services`|
|[Tahmin anahtarı](#prediction-endpoint-runtime-key)| Sorgu tahmini uç noktası istekleri. İstemci uygulamanızın, başlatıcı kaynak tarafından belirtilen 1.000 isteklerin ötesinde tahmin etmek için bir LUSıS tahmin anahtarı oluşturun. |`LUIS`|`Cognitive Services`|
|[Bilişsel hizmet hizmeti çoklu hizmet kaynak anahtarı](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|LUVE diğer desteklenen bilişsel hizmetler ile paylaşılan sorgu tahmini uç noktası istekleri.|`CognitiveServices`|`Cognitive Services`|

Kaynak oluşturma işlemi tamamlandığında, anahtarı HALU portalında uygulamaya [atayın](luis-how-to-azure-subscription.md) .

LUSıS uygulamalarını yayımlamak ve sorgulamak istediğiniz [bölgelerde](luis-reference-regions.md#publishing-regions) yazmak önemlidir.

> [!CAUTION]
> Kolaylık olması için, çoğu örnek, [kotasında](luis-boundaries.md#key-limits)birkaç ücretsiz tahmin uç noktası çağrısı sağladığından [Başlangıç anahtarını](#starter-key) kullanır.  


### <a name="query-prediction-resources"></a>Sorgu tahmini kaynakları

* Çalışma zamanı anahtarı tüm LUSıS uygulamalarınız veya belirli bir LUL Apps için kullanılabilir. 
* LUSıS uygulamalarını yazmak için çalışma zamanı anahtarını kullanmayın. 

LUıN çalışma zamanı uç noktası iki sorgu stilini kabul eder, her ikisi de tahmin uç noktası çalışma zamanı anahtarını kullanır, ancak farklı yerlerde.

Çalışma zamanına erişmek için kullanılan uç nokta, aşağıdaki tabloda `{region}` belirtilen kaynak bölgesi için benzersiz olan bir alt etki alanı kullanır. 

## <a name="assignment-of-the-key"></a>Anahtar ataması

Çalışma zamanı anahtarını [LUIS portalında](https://www.luis.ai) veya Ilgili API 'ler aracılığıyla [atayabilirsiniz](luis-how-to-azure-subscription.md) . 

## <a name="key-limits"></a>Anahtar sınırları

Her abonelik için bölge başına en fazla 10 yazma anahtarı oluşturabilirsiniz. 

Bkz. [anahtar sınırları](luis-boundaries.md#key-limits) ve [Azure bölgeleri](luis-reference-regions.md). 

Yayımlama bölgeleri, yazma bölgelerinden farklıdır. İstemci uygulamanızın bulunmasını istediğiniz yayımlama bölgesine karşılık gelen yazma bölgesinde bir uygulama oluşturduğunuzdan emin olun.

## <a name="key-limit-errors"></a>Anahtar sınırı hataları
İşlem/saniye (TPS) kotasını aşarsanız bir HTTP 429 hatası alırsınız. İşlem/saat (TPS) kotasını aşarsanız bir HTTP 403 hatası alırsınız. 

## <a name="contributions-from-other-authors"></a>Diğer yazarlardan katkı

**[Kaynak geçişi](luis-migration-authoring.md) yapılan uygulamalar yazmak için**: _katkıda bulunanlar_ , **Access Control (IAM)** sayfasını kullanarak yazma kaynağı için Azure Portal yönetilir. Ortak çalışan e-posta adresini ve _katkıda_ bulunan rolünü kullanarak [bir Kullanıcı eklemeyi](luis-how-to-collaborate.md)öğrenin. 

**Henüz geçirilmeyen uygulamalar için**: tüm _ortak çalışanlar_ , **Manage-> ortak çalışanlar** sayfasından Luo portalında yönetilir.

## <a name="move-transfer-or-change-ownership"></a>Taşıma, aktarma veya sahipliği değiştirme

Bir uygulama, Azure kaynakları tarafından tanımlanır ve bu, sahibin aboneliğine göre belirlenir. 

LUSıS uygulamanızı taşıyabilirsiniz. Azure portal veya Azure CLı 'de aşağıdaki belge kaynaklarını kullanın:

* [Uygulamayı LUSıS yazma kaynakları arasında taşıma](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Kaynağı yeni kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/resource-group-move-resources.md)
* [Kaynağı aynı abonelik içinde veya abonelikler arasında taşıma](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)

Aboneliğinizin [sahipliğini](../../billing/billing-subscription-transfer.md) aktarmak için: 

**Geçirilen [kaynak geçişi](luis-migration-authoring.md) olan uygulamalar için**: kaynak sahibi olarak, `contributor`ekleyebilirsiniz.

**Henüz geçirilmeyen kullanıcılar için**: UYGULAMANıZı bir JSON dosyası olarak dışarı aktarın. Başka bir LUO kullanıcısı uygulamayı içeri aktarabilir, böylece uygulama sahibi olur. Yeni uygulamanın farklı bir uygulama KIMLIĞI olacaktır.  

## <a name="access-for-private-and-public-apps"></a>Özel ve genel uygulamalara erişim

**Özel** bir uygulama için çalışma zamanı erişimi, sahipler ve katkıda bulunanlar için kullanılabilir. **Ortak** bir uygulama için, çalışma zamanı erişimi kendi Azure bilişsel [hizmeti](../cognitive-services-apis-create-account.md) veya [lusıs](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) çalışma zamanı kaynağı olan herkese açıktır ve ortak uygulamanın kimliğine sahiptir. 

Şu anda, ortak uygulamaların bir kataloğu yoktur.

### <a name="authoring-access"></a>Yazma erişimi
[LUIS](luis-reference-regions.md#luis-website) portalından uygulamaya erişim veya [yazma API 'leri](https://go.microsoft.com/fwlink/?linkid=2092087) , Azure yazma kaynağı tarafından denetlenir. 

Sahibi ve tüm katkıda bulunanlar, uygulamayı yazmak için erişime sahiptir. 

|Yazma erişimi şunları içerir|Notlar|
|--|--|
|Uç nokta anahtarları ekleme veya kaldırma||
|Sürüm dışarı aktarılıyor||
|Uç nokta günlüklerini dışarı aktar||
|Sürüm içeri aktarılıyor||
|Uygulamayı genel yap|Bir uygulama genel olduğunda, yazma veya bitiş noktası anahtarına sahip herkes uygulamayı sorgulayabilir.|
|Modeli değiştir|
|Yayımlama|
|[Etkin öğrenme](luis-how-to-review-endpoint-utterances.md) için uç nokta utslerini gözden geçirme|
|Eğitim|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Tahmin uç noktası çalışma zamanı erişimi

Tahmin uç noktası sorgulama erişimi, **Yönet** bölümündeki **uygulama bilgileri** sayfasında bir ayarla denetlenir. 

|[Özel uç nokta](#runtime-security-for-private-apps)|[Ortak uç nokta](#runtime-security-for-public-apps)|
|:--|:--|
|Sahip ve katkıda bulunanlar için kullanılabilir|Uygulama KIMLIĞINI bilen sahip, katkıda bulunanlar ve diğer kişiler için kullanılabilir|

Bir sunucudan sunucuya bir ortama çağırarak, LUSıS çalışma zamanı anahtarınızı kimlerin göreceğini denetleyebilirsiniz. Bir bot 'tan LUSıS kullanıyorsanız, bot ve LUYA arasındaki bağlantı zaten güvenlidir. LUU uç noktasını doğrudan arıyorsanız, denetimli erişime ( [AAD](https://azure.microsoft.com/services/active-directory/)gibi) sahip bir sunucu tarafı API (Azure [işlevi](https://azure.microsoft.com/services/functions/)gibi) oluşturmanız gerekir. Sunucu tarafı API 'SI çağrıldığında ve kimlik doğrulaması yapıldığında ve yetkilendirme doğrulandığında, çağrısı HALSıS 'e geçirin. Bu strateji, ortadaki adam saldırıları engellemez, ancak kullanıcılarınızın anahtar ve uç nokta URL 'nizi, kullanıcılarınızı izlemenizi sağlar ve uç nokta yanıt günlüğü (örneğin, [Application Insights](https://azure.microsoft.com/services/application-insights/)) eklemenize olanak tanır.

#### <a name="runtime-security-for-private-apps"></a>Özel uygulamalar için çalışma zamanı güvenliği

Özel bir uygulamanın çalışma zamanı yalnızca şunlar için kullanılabilir:

|Anahtar ve Kullanıcı|Açıklama|
|--|--|
|Sahibin yazma anahtarı| En fazla 1000 uç nokta okuması|
|Ortak çalışan/katkıda bulunan yazma anahtarları| En fazla 1000 uç nokta okuması|
|LUŞUNA atanan herhangi bir anahtar, bir yazar veya ortak çalışan/katkıda bulunan|Anahtar kullanımı katmanına göre|

#### <a name="runtime-security-for-public-apps"></a>Ortak uygulamalar için çalışma zamanı güvenliği

Bir uygulama ortak olarak yapılandırıldıktan sonra, anahtarın _tüm uç_ nokta kotasını kullanmadığı sürece, GEÇERLI bir luya yazma anahtarı veya Luo uç nokta anahtarı uygulamanızı sorgulayabilir.

Sahibi veya katkıda bulunan olmayan bir Kullanıcı, uygulama KIMLIĞI verildiğinde yalnızca ortak uygulamanın çalışma zamanına erişebilir. LUYA genel bir uygulama aramak için genel bir _Pazar_ veya başka bir yol yoktur.  

Ortak bir uygulama tüm bölgelerde yayımlanır, böylece bölge tabanlı bir LUTO kaynak anahtarı olan bir Kullanıcı, kaynak anahtarıyla ilişkili bölgede bulunan uygulamaya erişebilir.

## <a name="transfer-of-ownership"></a>Sahiplik aktarımı

LUSıS 'in bir kaynağın sahipliğini aktarma kavramı yoktur. 

## <a name="securing-the-endpoint"></a>Uç noktanın güvenliğini sağlama 

Bir sunucudan sunucuya bir ortama çağırarak, LUSıS tahmin çalışma zamanı uç noktası anahtarınızı kimlerin görebileceğini denetleyebilirsiniz. Bir bot 'tan LUSıS kullanıyorsanız, bot ve LUYA arasındaki bağlantı zaten güvenlidir. LUU uç noktasını doğrudan arıyorsanız, denetimli erişime ( [AAD](https://azure.microsoft.com/services/active-directory/)gibi) sahip bir sunucu tarafı API (Azure [işlevi](https://azure.microsoft.com/services/functions/)gibi) oluşturmanız gerekir. Sunucu tarafı API 'SI çağrıldığında ve kimlik doğrulaması ve yetkilendirme doğrulandıktan sonra, çağrıyı HALSıS 'e geçirin. Bu strateji, ortadaki adam saldırıları engellemez, ancak uç noktanızı kullanıcılarınıza göre gizleme, erişimi izlemenize ve uç nokta yanıt günlüğü (örneğin [Application Insights](https://azure.microsoft.com/services/application-insights/)) eklemenize olanak tanır.  

## <a name="next-steps"></a>Sonraki adımlar

* [Sürüm oluşturma](luis-concept-version.md) kavramlarını anlayın. 
* [Anahtar oluşturmayı](luis-how-to-azure-subscription.md)öğrenin.

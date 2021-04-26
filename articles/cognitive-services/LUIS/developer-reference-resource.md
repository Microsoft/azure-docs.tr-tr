---
title: Geliştirici kaynakları-Language Understanding
description: SDK 'Lar, REST API 'Leri, CLı, programlama dilinizde Language Understanding (LUıS) uygulamaları geliştirmenize yardımcı olur. Azure kaynaklarınızı ve LUSıS tahminlerinizi yönetin.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 04c7d4a7e725d99c7dba94779d365312f8b960af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787116"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Language Understanding için SDK, REST ve CLı geliştirici kaynakları (LUSıS)

SDK 'Lar, REST API 'Leri, CLı, programlama dilinizde Language Understanding (LUıS) uygulamaları geliştirmenize yardımcı olur. Azure kaynaklarınızı ve LUSıS tahminlerinizi yönetin.

## <a name="azure-resource-management"></a>Azure Kaynak Yönetimi

Language Understanding veya bilişsel hizmet kaynağını oluşturmak, düzenlemek, listelemek ve silmek için Azure bilişsel hizmetler yönetim katmanını kullanın.

Araca göre başvuru belgelerini bulun:

* [Azure CLI](/cli/azure/cognitiveservices#az_cognitiveservices_list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding yazma ve tahmin istekleri

Language Understanding hizmetine, oluşturmanız gereken bir Azure kaynağından erişilir. İki kaynak vardır:

* Oluşturma, düzenleme, eğitme ve yayımlama için eğitim için **yazma** kaynağını kullanın.
* Kullanıcının metnini göndermek ve tahmin almak için çalışma zamanı **tahminini** kullanın.

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında bilgi edinin.

En yaygın görevleri öğrenmek ve kullanmak için bilişsel [Hizmetler örnek kodunu](https://github.com/Azure-Samples/cognitive-services-quickstart-code) kullanın.

### <a name="rest-specifications"></a>REST belirtimleri

[LUSıS Rest belirtimleri](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), tüm [Azure Rest belirtimleriyle](https://github.com/Azure/azure-rest-api-specs)birlikte GitHub 'da herkese açık bir şekilde sunulmaktadır.

### <a name="rest-apis"></a>REST API'leri

REST API 'lerden hem yazma hem de tahmin uç nokta API 'LERI kullanılabilir:

|Tür|Sürüm|
|--|--|
|Yazma|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[izleme v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Tahmin|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Yüklemesinde](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST uç noktaları

LUIN Şu anda 2 uç nokta türü vardır:

* Eğitim uç noktasında **yazma**
* çalışma zamanı uç noktasında sorgu **Tahmini** .

|Amaç|URL|
|--|--|
|Eğitim uç noktasında v2 yazma|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Eğitim uç noktasında v3 yazma|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2 tahmini-çalışma zamanı uç noktasındaki tüm tahminler|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 tahmin-çalışma zamanı uç noktasındaki sürümler tahmini|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 tahmini-çalışma zamanı uç noktasında yuva tahmini|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

Aşağıdaki tabloda, önceki tabloda küme ayraçları ile belirtilen parametreler açıklanmaktadır `{}` .

|Parametre|Amaç|
|--|--|
|`your-resource-name`|Azure Kaynak adı|
|`q` veya `query`|sohbet bot gibi istemci uygulamasından gönderilen söylenişi metni|
|`version`|10 karakter sürümü adı|
|`slot`| `production` veya `staging`|

### <a name="rest-query-string-parameters"></a>REST sorgu dizesi parametreleri

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Uygulama şeması

[Uygulama şeması](app-schema-definition.md) içeri aktarılır ve bir `.json` veya `.lu` biçiminde aktarılır.

### <a name="language-based-sdks"></a>Dil tabanlı SDK 'lar

|Dil |Başvuru belgeleri|Paket|Hızlı Başlangıçlar|
|--|--|--|--|
|C#|[Yazma](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Hızlı](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[NuGet yazma](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet tahmini](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Yazma](./client-libraries-rest-api.md?pivots=rest-api)<br>[Sorgu tahmini](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Yazma ve tahmin](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|['SıNıN](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Yazma ve tahmin](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Maven yazma](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven tahmini](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Yazma](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Hızlı](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[NPM yazma](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM tahmini](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Yazma](./client-libraries-rest-api.md?pivots=rest-api)<br>[Hızlı](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Yazma ve tahmin](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Yazma](./client-libraries-rest-api.md?pivots=rest-api)<br>[Hızlı](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Kapsayıcılar

Language Understanding (LUSıS), uygulamanızın şirket içi ve içerilen sürümlerini sağlamak için bir [kapsayıcı](luis-container-howto.md) sağlar.

### <a name="export-and-import-formats"></a>Dışarı ve içeri aktarma biçimleri

Language Understanding, uygulamanızı ve modellerini bir JSON biçiminde, `.LU` ([luaşağı](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) biçimde ve Language Understanding kapsayıcısı için sıkıştırılmış bir pakette yönetme olanağı sağlar.

Bu biçimlerin içeri ve dışarı aktarılması API 'lerden ve LUıS portalından kullanılabilir. Portal, uygulamalar listesi ve sürümler listesinin bir parçası olarak içeri ve dışarı aktarma sağlar.

## <a name="workshops"></a>Atölyeler

* GitHub: (Workshop) [konuşma-AI: NLU, LUL kullanma](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Sürekli tümleştirme araçları

* GitHub: (Önizleme) [DevOps uygulamalarını kullanarak BIR lusıs uygulaması geliştirme](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -NLU Hizmetleri için sürekli tümleştirmeyi ve dağıtımı destekleyen araçlar.

## <a name="bot-framework-tools"></a>Bot Framework araçları

Bot Framework, [Azure bot hizmeti](https://dev.botframework.com/)kullanılarak çeşitli dillerde ve hizmet olarak bir [SDK](https://github.com/Microsoft/botframework) olarak kullanılabilir.

Bot Framework, aşağıdakiler dahil olmak üzere Language Understanding yardımcı olmak için [çeşitli araçlar](https://github.com/microsoft/botbuilder-tools) sağlar:
* [Bot Framework öykünücüsü](https://github.com/Microsoft/BotFramework-Emulator/releases) -bot geliştiricilerinin, bot Framework SDK kullanılarak oluşturulan botları test etmesine ve hata ayıklamasına izin veren bir masaüstü uygulaması
* [Bot Framework besteci](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) -geliştiriciler ve çok disiplinli takımlar Için Microsoft bot Framework ile botları ve konuşma deneyimleri oluşturmaya yönelik tümleşik bir geliştirme aracı
* [Bot Framework örnekleri](https://github.com/microsoft/botbuilder-samples) -#C, JavaScript, TypeScript ve Python

## <a name="next-steps"></a>Sonraki adımlar

* Ortak [http hata kodları](luis-reference-response-codes.md) hakkında bilgi edinin
* Tüm API 'Ler ve SDK 'Lar için [başvuru belgeleri](../../index.yml)
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) ve [Azure bot hizmeti](https://dev.botframework.com/)
* [Luaşağı](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Bilişsel kapsayıcılar](../cognitive-services-container-support.md)

---
title: Geliştirici kaynakları-Language Understanding
titleSuffix: Azure Cognitive Services
description: Geliştiricilerin hem REST API 'Leri hem de Language Understanding SDK 'Ları vardır.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d59646a87727409d759cc1903046fb3cdeade2e0
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847383"
---
# <a name="developer-resources-for-language-understanding"></a>Language Understanding için geliştirici kaynakları

Geliştiriciler, Language Understanding için hem REST API 'Leri hem de SDK 'Ları kullanabilir. 

## <a name="azure-resource-management"></a>Azure Kaynak Yönetimi

Language Understanding veya bilişsel hizmet kaynağını oluşturmak, düzenlemek, listelemek ve silmek için Azure bilişsel hizmetler yönetim katmanını kullanın.

Araca göre başvuru belgelerini bulun:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding yazma ve tahmin istekleri

Language Understanding hizmetine, oluşturmanız gereken bir Azure kaynağından erişilir. İki kaynak vardır: yazma ve tahmin uç noktası kaynakları. Bu kaynakların her ikisi de LUSıS kaynaklarınızı denetlemenize olanak tanır. 

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında bilgi edinin.

### <a name="rest-apis"></a>REST API'leri

REST API 'lerden hem yazma hem de tahmin uç nokta API 'LERI kullanılabilir:

|Tür|Sürüm|
|--|--|
|Yazma|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[izleme v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Hızlı|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Yüklemesinde](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>Dil tabanlı SDK 'lar

|Dil |Başvuru belgeleri|Paket|Örnekler|Hızlı Başlangıçlar|
|--|--|--|--|--|
|C#|[Yazma](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Tahmin](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet yazma](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet tahmini](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.NET SDK örnekleri](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Uygulama oluşturma ve yönetme](sdk-csharp-quickstart-authoring-app.md)<br>[Sorgu tahmin uç noktası](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[Yazma ve tahmin](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Yazma](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Tahmin](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[REST kullanarak yazma](luis-get-started-go-add-utterance.md)<br>[REST kullanarak tahmin](luis-get-started-go-get-intent.md)|
|Java|[Yazma ve tahmin](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven yazma](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven tahmini](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Yazma](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Tahmin](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Yazma](luis-get-started-java-add-utterance.md)<br>[Tahmin](luis-get-started-java-get-intent.md)
|Node.js|[Yazma](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Tahmin](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM yazma](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM tahmini](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Yazma](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Tahmin](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[REST kullanarak yazma](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[REST kullanarak tahmin](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[Yazma ve tahmin](sdk-python-quickstart-authoring-app.md)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Yazma](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Yazma](sdk-python-quickstart-authoring-app.md)<br>[REST kullanarak tahmin](luis-get-started-python-get-intent.md)


### <a name="containers"></a>Kapsayıcılar

Language Understanding (LUSıS), uygulamanızın şirket içi ve içerilen sürümlerini sağlamak için bir [kapsayıcı](luis-container-howto.md) sağlar. 

### <a name="export-and-import-formats"></a>Dışarı ve içeri aktarma biçimleri

Language Understanding, uygulamanızı ve modellerini bir JSON biçiminde, `.LU` ([Luaşağı](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) biçimde ve Language Understanding kapsayıcısı için sıkıştırılmış bir pakette yönetme olanağı sağlar. 

Bu biçimlerin içeri ve dışarı aktarılması API 'lerden ve LUıS portalından kullanılabilir. Portal, uygulamalar listesi ve sürümler listesinin bir parçası olarak içeri ve dışarı aktarma sağlar. 

## <a name="other-tools-and-sdks"></a>Diğer araçlar ve SDK 'lar

Bot Framework, [Azure bot hizmeti](https://dev.botframework.com/)kullanılarak çeşitli dillerde ve hizmet olarak bir [SDK](https://github.com/Microsoft/botframework) olarak kullanılabilir. 

Bot Framework, aşağıdakiler dahil olmak üzere Language Understanding yardımcı olmak için [çeşitli araçlar](https://github.com/microsoft/botbuilder-tools) sağlar:

* [Luaşağı](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -derleme lusıs dili, markın dosyalarını kullanarak modelleri anlama
* [LUSıS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) -LUIS.ai uygulamalarınızı oluşturma ve yönetme
* [Dağıtım](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-üst ve alt uygulamaları yönetme
* [Luisgen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -lusıs amaçları C#ve varlıklarınız için otomatik olarak yedekleme/TypeScript sınıfları oluşturun.
* [Bot öykünücüsü](https://github.com/Microsoft/BotFramework-Emulator/releases) -bot geliştiricilerinin, bot Framework SDK kullanılarak oluşturulan botları test etmesine ve hata ayıklamasına izin veren bir masaüstü uygulaması


## <a name="next-steps"></a>Sonraki adımlar

* Ortak [http hata kodları](luis-reference-response-codes.md) hakkında bilgi edinin
* Tüm API 'Ler ve SDK 'Lar için [başvuru belgeleri](https://docs.microsoft.com/azure/index#pivot=sdkstools)
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) ve [Azure bot hizmeti](https://dev.botframework.com/)
* [Luaşağı](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Bilişsel kapsayıcılar](../cognitive-services-container-support.md)
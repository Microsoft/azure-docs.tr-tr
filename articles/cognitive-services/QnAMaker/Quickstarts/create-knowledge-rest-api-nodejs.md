---
title: "Hızlı başlangıç: node. js için REST API 'lerle Soru-Cevap Oluşturma"
titleSuffix: Azure Cognitive Services
description: Node. js için Soru-Cevap Oluşturma REST API 'Leri ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin.  Soru-Cevap Oluşturma, SSS belgeleri, URL'ler ve ürün kılavuzları gibi yarı yapılandırılmış içeriklerinizden bir soru cevap hizmeti oluşturmanızı sağlar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 4393609bf426c6ae99c48a5d84162526aeff6fb7
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803511"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Hızlı başlangıç: node. js için REST API 'Leri Soru-Cevap Oluşturma

Node. js için Soru-Cevap Oluşturma REST API 'Leri ile çalışmaya başlayın. Temel görevler için örnek kodu denemek üzere bu adımları izleyin.  Soru-Cevap Oluşturma, SSS belgeleri, URL'ler ve ürün kılavuzları gibi yarı yapılandırılmış içeriklerinizden bir soru cevap hizmeti oluşturmanızı sağlar. 

Node. js için Soru-Cevap Oluşturma REST API 'Lerini kullanarak şunları yapın:

* Bilgi bankası oluşturun
* Bilgi bankasını değiştirme
* Bilgi bankası yayımlama
* Bilgi bankasını silme
* Bilgi Bankası indirin
* İşlemin durumunu al

[Başvuru belgeleri](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node. js örnekleri](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [Node. js](https://nodejs.org)' nin geçerli sürümü.
* [Soru-Cevap Oluşturma hizmetine](../How-To/set-up-qnamaker-service-azure.md) sahip olmanız gerekir. Anahtarınızı ve uç noktanızı (kaynak adını da içerir) almak için Azure portal kaynağınız için **hızlı başlangıç** ' ı seçin.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-qna-maker-azure-resource"></a>Soru-Cevap Oluşturma Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak soru-cevap oluşturma için bir kaynak oluşturun. 

Kaynağından bir anahtar aldıktan sonra, kaynak için `QNAMAKER_RESOURCE_KEY` ve `QNAMAKER_AUTHORING_ENDPOINT` adlı [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) . Azure portal kaynağın **hızlı başlangıç** sayfasında bulunan anahtar ve uç nokta değerlerini kullanın.

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

Bir Node `package.json` dosyası oluşturmak için `npm init -y` komutunu çalıştırın. 

```console
npm init -y
```

@No__t-0 ve `request` NPM paketlerini ekleyin:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Node. js için Soru-Cevap Oluşturma REST API 'Leri ile aşağıdakilerin nasıl yapılacağını gösterir:

* [Bilgi bankası oluşturma](#create-a-knowledge-base)
* [Bilgi bankasını değiştirme](#replace-a-knowledge-base)
* [Bilgi bankası yayımlama](#publish-a-knowledge-base)
* [Bilgi Bankası silme](#delete-a-knowledge-base)
* [Bilgi Bankası indirin](#download-the-knowledge-base)
* [İşlemin durumunu al](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Bağımlılıkları ekleme



@No__t-0 adlı bir dosya oluşturun ve HTTP istekleri oluşturmak için aşağıdaki _gerekli_ ifadeyi ekleyin. 

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Azure Kaynak bilgilerini ekleme

Kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun. Uygulamayı başlattıktan sonra ortam değişkenini oluşturduysanız, değişkene erişmek için onu çalıştıran düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Bilgi bankası oluşturun

Bilgi Bankası, bir JSON nesnesinden oluşturulan soru ve yanıt çiftlerini depolar:

* **Düzenleme içeriği**. 
* **Dosyalar** -herhangi bir izin gerektirmeyen yerel dosyalar. 
* **URL 'ler** -genel olarak kullanılabilir URL 'ler.

[Bilgi Bankası oluşturmak için REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)kullanın. 

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Bilgi bankasını değiştirme

[Bilgi bankasını değiştirmek için REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)kullanın.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Bilgi bankası yayımlama

Bilgi Bankası 'nı yayımlayın. Bu işlem, bilgi bankasını bir HTTP sorgu tahmin uç noktasından kullanılabilir hale getirir.

[Bilgi Bankası yayımlamak için REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)kullanın.


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Bilgi Bankası 'nı indirin 

[Bilgi Bankası indirmek için REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)kullanın.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Bilgi bankasını silme

Bilgi Bankası 'nı kullanarak işiniz bittiğinde silin.

[Bilgi Bankası 'nı silmek için REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)kullanın.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>İşlemin durumunu al

Oluşturma işlemi gibi uzun süre çalışan işlemler, ayrı bir REST API çağrısıyla denetlenmesi gereken bir işlem KIMLIĞI döndürür. Bu işlev, oluşturma yanıtının gövdesini alır. Önemli anahtar, yoklamaya devam etmeniz gerektiğini belirleyen `operationState` ' dır.

[Bilgi Bankası 'ndaki işlemleri izlemek için REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)kullanın.


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı uygulama dizininizdeki `node rest-apis.js` komutuyla çalıştırın.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Öğretici: bir KB oluşturun ve yanıtlayın](../tutorials/create-publish-query-in-portal.md)

* [Soru-Cevap Oluşturma API'si nedir?](../Overview/overview.md)
* [Bilgi Bankası düzenleme](../how-to/edit-knowledge-base.md)
* [Kullanım analizlerini al](../how-to/get-analytics-knowledge-base.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js)' da bulunabilir.
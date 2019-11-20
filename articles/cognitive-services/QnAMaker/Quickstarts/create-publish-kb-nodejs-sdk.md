---
title: 'Hızlı başlangıç: node. js için Soru-Cevap Oluşturma istemci kitaplığı'
titleSuffix: Azure Cognitive Services
description: Node. js için Soru-Cevap Oluşturma istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin.  Soru-Cevap Oluşturma, SSS belgeleri, URL'ler ve ürün kılavuzları gibi yarı yapılandırılmış içeriklerinizden bir soru cevap hizmeti oluşturmanızı sağlar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 7650dce9a3ef494815b8d7a326eb07e1e25f2da2
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123099"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>Hızlı başlangıç: node. js için Soru-Cevap Oluşturma istemci kitaplığı

Node. js için Soru-Cevap Oluşturma istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin.  Soru-Cevap Oluşturma, SSS belgeleri, URL'ler ve ürün kılavuzları gibi yarı yapılandırılmış içeriklerinizden bir soru cevap hizmeti oluşturmanızı sağlar. 

Node. js için Soru-Cevap Oluşturma istemci kitaplığını kullanarak şunları yapın:

* Bilgi bankası oluşturma 
* Bilgi Bankası 'nı yönetme
* Bilgi bankası yayımlama

[Node. js örnekleri](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) | [kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [paketi (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [başvuru belgeleri](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [Node. js](https://nodejs.org)' nin geçerli sürümü.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-qna-maker-azure-resource"></a>Soru-Cevap Oluşturma Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak soru-cevap oluşturma için bir kaynak oluşturun. 

Kaynağından bir anahtar aldıktan sonra, kaynak için `QNAMAKER_SUBSCRIPTION_KEY` ve `QNAMAKER_HOST`adlı [ortam değişkenleri oluşturun](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) . Kaynağın **anahtarlarında** bulunan anahtar ve uç nokta değerlerini ve Azure Portal **genel bakış** sayfalarını kullanın.

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

Bir `package.json` dosyası ile bir düğüm uygulaması oluşturmak için `npm init -y` komutunu çalıştırın. 

```console
npm init -y
```

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

`ms-rest-azure` ve `azure-cognitiveservices-qnamaker` NPM paketlerini yükler:

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

Uygulamanızın `package.json` dosyası bağımlılıklarla güncellenir.


## <a name="object-model"></a>Nesne modeli

Soru-Cevap Oluşturma istemcisi, anahtarınızı içeren ServiceClientCredentials kullanarak Azure 'da kimlik doğrulayan bir [Qnamakerclient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) nesnesidir.

İstemci oluşturulduktan sonra bilgi bankasını oluşturma, yönetme ve yayımlama hakkında [Bilgi Bankası](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) özelliğini kullanın. 

Bir JSON nesnesi göndererek bilgi bankaınızı yönetin. Anında işlemler için bir yöntem genellikle durumu gösteren bir JSON nesnesi döndürür. Uzun süre çalışan işlemler için yanıt, işlem KIMLIĞIDIR. İstemcisini çağırın [. ](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) [İsteğin durumunu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)öğrenmek için işlem kimliği Ile Operations. GetDetails yöntemi. 

 
## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Node. js için Soru-Cevap Oluşturma istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını gösterir:

* [Bilgi bankası oluşturma](#create-a-knowledge-base)
* [Bilgi bankası yükleme](#update-a-knowledge-base)
* [Bilgi bankası yayımlama](#publish-a-knowledge-base)
* [Bilgi Bankası silme](#delete-a-knowledge-base)
* [İşlemin durumunu al](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Bağımlılıkları ekleme

`index.js` adlı bir dosya oluşturun. Soru-Cevap Oluşturma kitaplığı ve Azure REST kitaplığı 'nı dosyaya ekleyin.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

Kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun. Uygulamayı başlattıktan sonra ortam değişkenini oluşturduysanız, değişkene erişmek için onu çalıştıran düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.

|Ortam değişkeni|Node. js değişkeni|Örnek|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|32 karakter GUID 'SI|
|`QNAMAKER_HOST`|`endpoint`|`https://your-resource-name.api.cognitive.microsoft.com`-alt etki alanı `your-resource-name`, kaynağınızın adı değeri ile değiştirin|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Ardından, anahtarınızla bir ServiceClientCredentials nesnesi oluşturun ve bunu bir [Qnamakerclient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) nesnesi oluşturmak için uç noktanızla birlikte kullanın. [Bilgi Bankası](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) nesnesi almak için istemci nesnesini kullanın.


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Bilgi bankası oluşturma

Bilgi Bankası, üç kaynaktan alınan [Createkbdto](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) nesnesine soru ve yanıt çiftlerini depolar:

* **Düzenleme içeriği**için [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) nesnesini kullanın.
* **Dosyalar**Için [filedto](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) nesnesini kullanın. 
* **URL 'ler**için bir dize listesi kullanın.

[Oluştur](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) metodunu çağırın ve ardından döndürülen işlem kimliğini durum yoklamak için [Operations. GetDetails](#get-status-of-an-operation) metoduna geçirin. 

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

Bir bilgi bankasını başarıyla oluşturmak için yukarıdaki kodda başvurulan [`wait_for_operation`](#get-status-of-an-operation) işlevinin dahil olduğundan emin olun. 

## <a name="update-a-knowledge-base"></a>Bilgi bankası güncelleştirme

Bilgi Bankası KIMLIĞI ve [güncelleştirme](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) yöntemi için [Add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [Update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)ve [Delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO nesnelerini içeren bir [updatekboperationdto](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) ile bir Bilgi Bankası güncelleştirebilirsiniz. Güncelleştirme başarılı olup olmadığını öğrenmek için [Operation. getDetail](#get-status-of-an-operation) metodunu kullanın.

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

Bilgi bankasını başarıyla güncelleştirmek için yukarıdaki kodda başvurulan [`wait_for_operation`](#get-status-of-an-operation) işlevinin dahil olduğundan emin olun. 

## <a name="publish-a-knowledge-base"></a>Bilgi bankası yayımlama

[Yayımla](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) yöntemini kullanarak Bilgi Bankası 'nı yayımlayın. Bu, Bilgi Bankası KIMLIĞI tarafından başvurulan geçerli kaydedilmiş ve eğitilen modeli alır ve bir uç noktada bunu yayınlar. 

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Bilgi bankasını silme

Bilgi Bankası KIMLIĞI parametresiyle [Delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) metodunu kullanarak Bilgi Bankası 'nı silin. 

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>İşlemin durumunu al

Oluşturma ve güncelleştirme gibi bazı yöntemler, işlemin tamamlanmasını beklemek yerine bir [işlem](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) döndürülür. Özgün yöntemin durumunu öğrenmek için işlem [kimliğini](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) yoklamaya (yeniden deneme mantığı ile) kullanın. 

Aşağıdaki kod bloğundaki _setTimeout_ çağrısı, zaman uyumsuz kodun benzetimini yapmak için kullanılır. Bunu yeniden deneme mantığı ile değiştirin. 

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı uygulama dizininizdeki `node index.js` komutuyla çalıştırın.


Bu makaledeki tüm kod parçacıkları [kullanılabilir](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) ve tek bir dosya olarak çalıştırılabilir.

```console
node index.js
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Öğretici: bir KB oluşturun ve yanıtlayın](../tutorials/create-publish-query-in-portal.md)

* [Soru-Cevap Oluşturma API'si nedir?](../Overview/overview.md)
* [Bilgi Bankası Düzenle](../how-to/edit-knowledge-base.md)
* [Kullanım analizlerini al](../how-to/get-analytics-knowledge-base.md)
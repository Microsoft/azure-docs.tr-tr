---
title: 'Hızlı başlangıç: node. js için kişiselleştirici istemci kitaplığı | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Bir öğrenme döngüsü kullanarak Node. js için kişiselleştirici istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: f7667ce99ceaa713332ad9419ffab9d57aa0f075
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494448"
---
# <a name="quickstart-personalizer-client-library-for-nodejs"></a>Hızlı başlangıç: node. js için kişiselleştirici istemci kitaplığı

Kişiselleştirici hizmeti ile bu Node. js hızlı başlangıçdaki kişiselleştirilmiş içeriği görüntüleyin.

Node. js için kişiselleştirici istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin.

 * Kişiselleştirmeye yönelik eylemlerin listesini sıralama.
 * En çok kullanılan derecelendirme eyleminin başarısını belirten rapor ödül.

[Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [paketi (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [örnekleri](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* Geçerli [Node. js](https://nodejs.org) ve NPM sürümü.

## <a name="using-this-quickstart"></a>Bu hızlı başlangıcı kullanma


Bu hızlı başlangıcı kullanmanın birkaç adımı vardır:

* Azure portal, bir kişiselleştirici kaynağı oluşturun
* Azure portal, kişiselleştirici kaynağı için **yapılandırma** sayfasında, model güncelleştirme sıklığını değiştirin
* Kod düzenleyicisinde bir kod dosyası oluşturun ve kod dosyasını düzenleyin
* Komut satırı veya terminalinde, komut satırından SDK 'Yı yükler
* Komut satırında veya terminalde, kod dosyasını çalıştırın


## <a name="create-a-personalizer-azure-resource"></a>Bir kişiselleştirici Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak kişiselleştirici için bir kaynak oluşturun. Aşağıdakileri de yapabilirsiniz:

* [Deneme anahtarını](https://azure.microsoft.com/try/cognitive-services) ücretsiz olarak 7 gün boyunca geçerli olacak şekilde öğrenin. Kaydolduktan sonra [Azure Web sitesinde](https://azure.microsoft.com/try/cognitive-services/my-apis/)mevcut olacaktır.  
* [Azure Portal](https://portal.azure.com/)kaynağı görüntüleyin.

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra, iki [ortam değişkeni](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)oluşturun:

* Kaynak anahtarı için `PERSONALIZER_KEY`.
* Kaynak uç noktası için `PERSONALIZER_ENDPOINT`.

Azure portal, hem anahtar hem de uç nokta değerleri **hızlı başlangıç** sayfasından kullanılabilir.


## <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

Bir `package.json` dosyası oluşturmak için `npm init -y` komutunu çalıştırın. 

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Kişiselleştirici için Node. js kitaplığını yükler

Aşağıdaki komutla Node. js için kişiselleştirici istemci kitaplığını yükler:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Bu hızlı başlangıç için kalan NPM paketlerini yükler:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="change-the-model-update-frequency"></a>Model güncelleştirme sıklığını değiştirme

Azure portal, **yapılandırma** sayfasındaki kişiselleştirici kaynağında, **model güncelleştirme sıklığını** 10 saniye olarak değiştirin. Bu, hizmeti hızlı bir şekilde eğitecektir ve her yineleme için en iyi eylem değişikliğini görmenizi sağlar.

![Model güncelleştirme sıklığını Değiştir](./media/settings/configure-model-update-frequency-settings.png)

Bir kişiselleştirici döngüsü ilk kez oluşturulduğunda, üzerinden eğitelenecek bir API çağrısı olmadığından model yoktur. Sıralama çağrıları her öğe için eşit olasılıklara dönüşe sahip olur. Uygulamanız hala Rewarterctionıd çıkışını kullanarak içeriği her zaman derecelendirmelidir.

## <a name="object-model"></a>Nesne modeli

Kişiselleştirici istemci, anahtarınızı içeren Microsoft. Rest. ServiceClientCredentials kullanarak Azure 'da kimlik doğrulaması yapan bir Personizerclient nesnesidir.

İçeriğin bir derecesini sormak için bir RankRequest oluşturun ve ardından istemciye geçirin. Rank yöntemi. Rank yöntemi, derecelendirilen içeriği içeren bir RankResponse döndürür. 

Kişiselleştiriciye bir ödül göndermek için bir RewardRequest oluşturun ve ardından istemciye geçirin. Reward yöntemi. 

Bu hızlı başlangıçta önemsiz bir şekilde belirlenir. Bir üretim sisteminde, İleri [puanı](concept-rewards.md) neyin etkilediğini ve ne kadar karmaşık bir süreç olabileceğini belirleme, zaman içinde değiştirmeye karar verebilirsiniz. Bu, kişiselleştirici mimarinizdeki birincil tasarım kararlarından biri olmalıdır. 

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Node. js için kişiselleştirici istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını gösterir:

* [Bir kişiselleştirici istemci oluşturma](#create-a-personalizer-client)
* [Bir derece iste](#request-a-rank)
* [Bir ödül gönderin](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Tercih ettiğiniz düzenleyicide veya IDE 'de `sample.js`adlı yeni bir Node. js uygulaması oluşturun. 

## <a name="add-the-dependencies"></a>Bağımlılıkları ekleme

**Örnek. js** dosyasını tercih ettiğiniz DÜZENLEYICIDE veya IDE 'de açın. NPM paketlerini eklemek için aşağıdaki `requires` ekleyin:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Kişiselleştirici kaynak bilgileri ekleme

`PERSONALIZER_KEY` ve `PERSONALIZER_ENDPOINT`adlı ortam değişkenlerinden çekilen kaynak Azure anahtarı ve uç nokta için değişkenler oluşturun. Uygulama başlatıldıktan sonra ortam değişkenlerini oluşturduysanız, değişkene erişmek için bu uygulamayı çalıştıran düzenleyici, IDE veya kabuğun kapatılıp yeniden yüklenmesi gerekir. Yöntemler daha sonra bu hızlı başlangıçta oluşturulacaktır.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Bir kişiselleştirici istemci oluşturma

Sonra, bir kişiselleştirici istemci döndürmek için bir yöntem oluşturun. Yöntemine yönelik parametre `PERSONALIZER_RESOURCE_ENDPOINT` ve ApiKey `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Eylem olarak temsil edilen içerik seçimlerini al

Eylemler, Kişiselleştiriciye göre derecelendirmek istediğiniz içerik seçimlerini temsil eder. Komut satırından günün saati ve geçerli yiyecek tercihi için bir kullanıcının girişini almak üzere program sınıfına aşağıdaki yöntemleri ekleyin.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Öğrenme döngüsünü oluşturma

Kişiselleştirici öğrenme döngüsü, bir [derece](#request-a-rank) ve [yeniden](#send-a-reward) arama çağrısı döngüsüdür. Bu hızlı başlangıçta, içeriği kişiselleştirmek için her bir derecelendirme çağrısı, kişiselleştirmeden hizmetin içeriği derecelendirerek ne kadar iyi bir şekilde bilgi verdiğini söyleyerek bir ödül çağrısıyla izlenir. 

Aşağıdaki döngü kodu, kullanıcıdan komut satırında tercihlerini sorma, bu bilgileri kişisel olarak sıralama için gönderme, derecelendirme seçimini bir liste arasından seçim yapmak üzere müşteriye sunma ve sonra bir yeniden Kişiselleştirici, hizmetin seçimi derecelendirerek ne kadar iyi olduğunu işaret ediyor.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Aşağıdaki bölümlerde bulunan derece ve ödül çağrılarına daha yakından göz atın.

Kod dosyasını çalıştırmadan önce [içerik seçimlerini almak](#get-content-choices-represented-as-actions)için aşağıdaki yöntemleri ekleyin:

* getActionsList
* getContextFeaturesList

## <a name="request-a-rank"></a>Bir derece iste

Sıralama isteğini tamamlayabilmeniz için, program kullanıcının tercihlerine içerik seçenekleri oluşturmasını ister. İşlem, `excludeActions`olarak gösterilen derecenin dışında tutulacak içerik oluşturabilir. Sıralama isteğinin, sıralanmış yanıtı almak için [Eylemler](concepts-features.md#actions-represent-a-list-of-options), CurrentContext, excludeactions ve benzersiz bir sıra olay KIMLIĞI (GUID olarak) gerekir. 

Bu hızlı başlangıçta, günün saati ve Kullanıcı yiyecek tercihi basit bağlam özelliklerine sahiptir. Üretim sistemlerinde, [eylemleri ve özellikleri](concepts-features.md) belirlemek ve [değerlendirmek](concept-feature-evaluation.md) önemsiz olmayan bir şekilde olabilir.  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Bir ödül gönderin

Yeniden isteği tamamlamaya yönelik olarak, program kullanıcının seçimini komut satırından alır, her seçime bir sayısal değer atar, sonra benzersiz sıralama olay kimliğini ve sayısal değeri ödül yöntemine gönderir.

Bu hızlı başlangıç, sıfır veya 1 olarak basit bir sayı atar. Üretim [sistemlerinde, bu çağrıya ne](concept-rewards.md) zaman ve ne gönderileceğini belirlemek, özel gereksinimlerinize bağlı olarak önemsiz olmayan bir önemi olabilir. 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Programı çalıştırma

Uygulamayı uygulama dizininizden Node. js ile çalıştırın.

```console
node sample.js
```

![Hızlı başlangıç programı, özellikler olarak bilinen Kullanıcı tercihlerini toplamak için birkaç soru sorar ve sonra en iyi eylemi sağlar.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bilişsel hizmetler aboneliğini temizlemek ve kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Kişiselleştirici nasıl kullanılır?](how-personalizer-works.md)

* [Kişiselleştirici nedir?](what-is-personalizer.md)
* [Kişiselleştirici 'yi nerede kullanabilirsiniz?](where-can-you-use-personalizer.md)
* [Sorun giderme](troubleshooting.md)
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)' da bulunabilir.

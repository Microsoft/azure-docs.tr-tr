---
title: 'Hızlı başlangıç: Python için kişiselleştirici istemci kitaplığı | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Bir öğrenme döngüsü kullanarak Python için kişiselleştirici istemci kitaplığı ile çalışmaya başlayın.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 02c4e0142ed7b3719cc07306f089769c532d6653
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494426"
---
# <a name="quickstart-personalizer-client-library-for-python"></a>Hızlı başlangıç: Python için kişiselleştirici istemci kitaplığı

Kişiselleştirilmiş içeriği bu Python hızlı penceresinde kişiselleştirici hizmeti ile görüntüleyin.

Python için kişiselleştirici istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek için bu adımları izleyin ve temel görevler için örnek kodu deneyin.

 * Kişiselleştirmeye yönelik eylemlerin listesini sıralama.
 * En çok kullanılan derecelendirme eyleminin başarısını belirten rapor ödül.

[Package (Pypı)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [örnekleri](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Bu hızlı başlangıcı kullanma


Bu hızlı başlangıcı kullanmanın birkaç adımı vardır:

* Azure portal, bir kişiselleştirici kaynağı oluşturun
* Azure portal, kişiselleştirici kaynağı için **yapılandırma** sayfasında, model güncelleştirme sıklığını değiştirin
* Kod düzenleyicisinde bir kod dosyası oluşturun ve kod dosyasını düzenleyin
* Komut satırı veya terminalinde, komut satırından SDK 'Yı yükler
* Komut satırında veya terminalde, kod dosyasını çalıştırın


## <a name="create-a-personalizer-azure-resource"></a>Bir kişiselleştirici Azure kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://portal.azure.com/) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak kişiselleştirici için bir kaynak oluşturun. Daha fazla ayrıntı için lütfen Azure portal kullanarak bilişsel [Hizmetler kaynağı oluşturma](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) bölümüne bakın. Aşağıdakileri de yapabilirsiniz:

* [Deneme anahtarını](https://azure.microsoft.com/try/cognitive-services) ücretsiz olarak 7 gün boyunca geçerli olacak şekilde öğrenin. Kaydolduktan sonra [Azure Web sitesinde](https://azure.microsoft.com/try/cognitive-services/my-apis/)mevcut olacaktır.  
* [Azure Portal](https://portal.azure.com/)kaynağı görüntüleyin.

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra, iki [ortam değişkeni](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)oluşturun:

* Kaynak anahtarı için `PERSONALIZER_KEY`.
* Kaynak uç noktası için `PERSONALIZER_ENDPOINT`.

Azure portal, hem anahtar hem de uç nokta değerleri **hızlı başlangıç** sayfasından kullanılabilir.


## <a name="install-the-python-library-for-personalizer"></a>Kişiselleştirici için Python kitaplığını yükler

Python için kişiselleştirici istemci kitaplığını aşağıdaki komutla birlikte yükler:

```console
pip install azure-cognitiveservices-personalizer
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

Bu kod parçacıkları, Python için kişiselleştirici istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını gösterir:

* [Bir kişiselleştirici istemci oluşturma](#create-a-personalizer-client)
* [Bir derece iste](#request-a-rank)
* [Bir ödül gönderin](#send-a-reward)

## <a name="create-a-new-python-application"></a>Yeni bir Python uygulaması oluşturma

Tercih ettiğiniz düzenleyicide veya IDE 'de `sample.py`adlı yeni bir Python uygulaması oluşturun. 

## <a name="add-the-dependencies"></a>Bağımlılıkları ekleme

Proje dizininden, **Sample.py** dosyasını tercih ettiğiniz DÜZENLEYICIDE veya IDE 'de açın. Aşağıdakileri ekleyin:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Kişiselleştirici kaynak bilgileri ekleme

`PERSONALIZER_RESOURCE_KEY` ve `PERSONALIZER_RESOURCE_ENDPOINT`adlı ortam değişkenlerinden çekilen kaynak Azure anahtarı ve uç nokta için değişkenler oluşturun. Uygulama başlatıldıktan sonra ortam değişkenlerini oluşturduysanız, değişkene erişmek için bu uygulamayı çalıştıran düzenleyici, IDE veya kabuğun kapatılıp yeniden yüklenmesi gerekir. Yöntemler daha sonra bu hızlı başlangıçta oluşturulacaktır.

Kaynak adı, uç nokta URL 'sinin bir parçası: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Bir kişiselleştirici istemci oluşturma

Sonra, bir kişiselleştirici istemci döndürmek için bir yöntem oluşturun. Yöntemine yönelik parametre `PERSONALIZER_RESOURCE_ENDPOINT` ve ApiKey `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Eylem olarak temsil edilen içerik seçimlerini al

Eylemler, Kişiselleştiriciye göre derecelendirmek istediğiniz içerik seçimlerini temsil eder. Günün saati ve geçerli yiyecek tercihi için komut satırından bir kullanıcının girişini almak üzere aşağıdaki yöntemleri ekleyin.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Öğrenme döngüsünü oluşturma

Kişiselleştirici öğrenme döngüsü, bir [derece](#request-a-rank) ve [yeniden](#send-a-reward) arama çağrısı döngüsüdür. Bu hızlı başlangıçta, içeriği kişiselleştirmek için her bir derecelendirme çağrısı, kişiselleştirmeden hizmetin içeriği derecelendirerek ne kadar iyi bir şekilde bilgi verdiğini söyleyerek bir ödül çağrısıyla izlenir. 

Aşağıdaki kod, kullanıcıya komut satırında tercihlerini sorma, bu bilgileri kişisel olarak derecelendirmaya gönderme, derecelendirme seçimini, liste arasından seçim yapmak üzere müşteriye sunma ve sonra kişiselleştirici için bir ödül gönderme amacıyla bir döngüyle geçer hizmetin seçimi derecelendirmesi ne kadar iyi olduğunu işaret edin.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Aşağıdaki bölümlerde bulunan derece ve ödül çağrılarına daha yakından göz atın.

Kod dosyasını çalıştırmadan önce [içerik seçimlerini almak](#get-content-choices-represented-as-actions)için aşağıdaki yöntemleri ekleyin:

* get_user_preference
* get_user_timeofday
* get_actions

## <a name="request-a-rank"></a>Bir derece iste

Program, derecelendirme isteğini gerçekleştirmek için kullanıcının tercihlerine içerik seçeneklerinin `currentContent` oluşturmasını ister. İşlem, `excludeActions`olarak gösterilen derecenin dışında tutulacak içerik oluşturabilir. Sıralama isteğinin, sıralanmış yanıtı almak için Eylemler, currentContext, excludeActions ve benzersiz bir sıra olay KIMLIĞI (GUID olarak) gerekir. 

Bu hızlı başlangıçta, günün saati ve Kullanıcı yiyecek tercihi basit bağlam özelliklerine sahiptir. Üretim sistemlerinde, [eylemleri ve özellikleri](concepts-features.md) belirlemek ve [değerlendirmek](concept-feature-evaluation.md) önemsiz olmayan bir şekilde olabilir.  

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Bir ödül gönderin

Yeniden isteği tamamlamaya yönelik olarak, program kullanıcının seçimini komut satırından alır, her seçime bir sayısal değer atar, sonra benzersiz sıralama olay kimliğini ve sayısal değeri ödül yöntemine gönderir.

Bu hızlı başlangıç, sıfır veya 1 olarak basit bir sayı atar. Üretim [sistemlerinde, bu çağrıya ne](concept-rewards.md) zaman ve ne gönderileceğini belirlemek, özel gereksinimlerinize bağlı olarak önemsiz olmayan bir önemi olabilir. 

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Programı çalıştırma

Uygulamayı uygulama dizininizdeki Python ile çalıştırın.

```console
python sample.py
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
* Bu örneğe ilişkin kaynak kodu [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)' da bulunabilir.

---
title: Authentication
titleSuffix: Azure Cognitive Services
description: 'Azure bilişsel hizmetler kaynağına yönelik bir isteğin kimlik doğrulamasının üç yolu vardır: abonelik anahtarı, bir taşıyıcı belirteç veya bir çoklu hizmet aboneliği. Bu makalede her bir yöntem ve bir istek oluşturma hakkında bilgi edineceksiniz.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: ae3530af7741b8ce48e08c2a85589ffae8a83f49
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276787"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Azure bilişsel hizmetler 'e yönelik isteklerin kimliğini doğrulama

Azure bilişsel hizmet hizmetine yapılan her istek bir kimlik doğrulama üst bilgisi içermelidir. Bu üst bilgi, bir hizmet veya hizmet grubu aboneliğinizi doğrulamak için kullanılan bir abonelik anahtarı veya erişim belirteci boyunca geçer. Bu makalede, bir isteğin kimliğini doğrulamak için üç yol ve her biri için gereksinimler hakkında bilgi edineceksiniz.

* [Tek servis abonelik anahtarı ile kimlik doğrulama](#authenticate-with-a-single-service-subscription-key)
* [Birden çok hizmet aboneliği anahtarıyla kimlik doğrulama](#authenticate-with-a-multi-service-subscription-key)
* [Belirteç ile kimlik doğrulama](#authenticate-with-an-authentication-token)
* [Azure Active Directory (AAD) ile kimlik doğrulama](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Önkoşullar

Bir istek yapmadan önce, bir Azure hesabı ve bir Azure bilişsel hizmetler aboneliğiniz olması gerekir. Zaten bir hesabınız varsa, devam edin ve sonraki bölüme atlayın. Hesabınız yoksa, birkaç dakika içinde ayarlamanıza yardımcı olacak bir kılavuz sunuyoruz: [Azure için bilişsel Hizmetler hesabı oluşturun](cognitive-services-apis-create-account.md).

Hesabınızı oluşturduktan sonra [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) abonelik anahtarınızı alabilir veya [ücretsiz denemeyi](https://azure.microsoft.com/try/cognitive-services/my-apis)etkinleştirebilirsiniz.

## <a name="authentication-headers"></a>Kimlik doğrulama üstbilgileri

Azure bilişsel hizmetler ile kullanılabilen kimlik doğrulama üstbilgilerini hızlıca gözden geçirelim.

| Üstbilgi | Açıklama |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Bu üstbilgiyi, belirli bir hizmet veya çok servis abonelik anahtarı için bir abonelik anahtarıyla kimlik doğrulaması yapmak için kullanın. |
| OCP-apim-Subscription-Region | Bu üst bilgi yalnızca [Translator metin çevirisi API'si](./Translator/reference/v3-0-reference.md)bir çoklu hizmet aboneliği anahtarı kullanılırken gereklidir. Abonelik bölgesini belirtmek için bu üstbilgiyi kullanın. |
| Authorization | Bir kimlik doğrulama belirteci kullanıyorsanız bu üstbilgiyi kullanın. Belirteç değişimi gerçekleştirme adımları aşağıdaki bölümlerde ayrıntılı olarak verilmiştir. Belirtilen değer şu biçimdedir: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Tek servis abonelik anahtarı ile kimlik doğrulama

İlk seçenek, Translator Metin Çevirisi gibi belirli bir hizmet için bir abonelik anahtarıyla bir isteğin kimliğini doğrular. Anahtarlar, oluşturduğunuz her kaynak için Azure portal kullanılabilir. Bir isteğin kimliğini doğrulamak üzere bir abonelik anahtarı kullanmak için, `Ocp-Apim-Subscription-Key` üst bilgi olarak geçirilmesi gerekir.

Bu örnek istekler, `Ocp-Apim-Subscription-Key` üstbilgiyi nasıl kullanacağınızı gösterir. Bu örneği kullanırken geçerli bir abonelik anahtarı eklemeniz gerektiğini aklınızda bulundurun.

Bu, Bing Web Araması API'si örnek bir çağrıdır:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Bu, Translator Metin Çevirisi API'si örnek bir çağrıdır:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Aşağıdaki videoda bilişsel hizmetler anahtarının kullanılması gösterilmektedir.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Birden çok hizmet aboneliği anahtarıyla kimlik doğrulama

>[!WARNING]
> Şu anda, **Bu hizmetler çoklu** hizmet anahtarlarını desteklemez: Soru-Cevap Oluşturma, konuşma Hizmetleri, Özel Görüntü İşleme ve anomali algılayıcısı.

Bu seçenek ayrıca isteklerin kimliğini doğrulamak için bir abonelik anahtarı kullanır. Temel fark, bir abonelik anahtarının belirli bir hizmete bağlı olmaması, bunun yerine, birden fazla bilişsel hizmet için isteklerin kimliğini doğrulamak için tek bir anahtar kullanılabilir. Bölgesel kullanılabilirlik, desteklenen özellikler ve fiyatlandırma hakkında bilgi için bkz. bilişsel [Hizmetler fiyatlandırması](https://azure.microsoft.com/pricing/details/cognitive-services/) .

Her istekte, `Ocp-Apim-Subscription-Key` üst bilgi olarak abonelik anahtarı sağlanır.

[![Bilişsel hizmetler için çoklu hizmet aboneliği anahtar gösterimi](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Desteklenen bölgeler

İstek yapmak için Multi-Service abonelik anahtarını kullanırken `api.cognitive.microsoft.com`, bu bölgeyi URL 'ye eklemeniz gerekir. Örneğin: `westus.api.cognitive.microsoft.com`

Translator metin çevirisi API'si birden çok servis abonelik anahtarı kullanırken, `Ocp-Apim-Subscription-Region` üst bilgiyle abonelik bölgesi belirtmeniz gerekir.

Bu bölgelerde çoklu hizmet kimlik doğrulaması desteklenir:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Örnek istekler

Bu, Bing Web Araması API'si örnek bir çağrıdır:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Bu, Translator Metin Çevirisi API'si örnek bir çağrıdır:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Kimlik doğrulama belirteciyle kimlik doğrulaması

Bazı Azure bilişsel hizmetler kabul eder ve bazı durumlarda bir kimlik doğrulama belirteci gerektirir. Şu anda bu hizmetler kimlik doğrulama belirteçlerini destekler:

* Metin çevirisi API 'SI
* Konuşma Hizmetleri: Konuşmayı metne REST API
* Konuşma Hizmetleri: Metin okuma REST API

>[!NOTE]
> Soru-Cevap Oluşturma Ayrıca yetkilendirme üst bilgisini kullanır, ancak bir uç nokta anahtarı gerektirir. Daha fazla bilgi için bkz [. soru-cevap oluşturma: Bilgi bankasından](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md)yanıt alın.

>[!WARNING]
> Kimlik doğrulama belirteçlerini destekleyen hizmetler zaman içinde değişebilir, lütfen bu kimlik doğrulama yöntemini kullanmadan önce bir hizmetin API başvurusunu denetleyin.

Kimlik doğrulama belirteçleri için hem tek hizmet hem de çok servis abonelik anahtarları değiştirilebilir. Kimlik doğrulama belirteçleri 10 dakika için geçerlidir.

Kimlik doğrulama belirteçleri bir isteğe `Authorization` üst bilgi olarak dahil edilir. Belirtilen belirteç değeri önünde `Bearer`olmalıdır, örneğin:. `Bearer YOUR_AUTH_TOKEN`

### <a name="sample-requests"></a>Örnek istekler

Kimlik doğrulama belirteci için bir abonelik anahtarı alışverişi yapmak üzere bu URL 'YI `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`kullanın:.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Bu çoklu hizmet bölgeleri belirteç değişimini destekler:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Bir kimlik doğrulama belirteci aldıktan sonra, her istekte onu `Authorization` üst bilgi olarak geçirmeniz gerekir. Bu, Translator Metin Çevirisi API'si örnek bir çağrıdır:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Ayrıca bkz.

* [Bilişsel Hizmetler nedir?](welcome.md)
* [Bilişsel Hizmetler fiyatlandırması](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Özel alt etki alanları](cognitive-services-custom-subdomains.md)

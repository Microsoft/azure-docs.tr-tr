---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70393836"
---
## <a name="set-up"></a>Ayarlama

### <a name="create-a-translator-text-resource"></a>Translator Metin Çevirisi kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. Yerel makinenizde [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) veya [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) kullanarak Translator metin çevirisi için bir kaynak oluşturun. Aşağıdakileri de yapabilirsiniz:

* [Deneme anahtarını](https://azure.microsoft.com/try/cognitive-services) ücretsiz olarak 7 gün boyunca geçerli olacak şekilde öğrenin. Kaydolduktan sonra Azure Web sitesinde mevcut olacaktır.
* [Azure Portal](https://portal.azure.com/)var olan bir kaynağı görüntüleyin.

Deneme aboneliğinizden veya kaynağından bir anahtar aldıktan sonra, iki [ortam değişkeni](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)oluşturun:

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`-Translator Metin Çevirisi kaynağınız için abonelik anahtarı.
* `TRANSLATOR_TEXT_ENDPOINT`-Translator Metin Çevirisi için genel uç nokta. Kullanın `https://api.cognitive.microsofttranslator.com/`.

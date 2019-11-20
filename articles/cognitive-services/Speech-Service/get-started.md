---
title: Konuşma hizmetini ücretsiz deneyin
titleSuffix: Azure Cognitive Services
description: Konuşma hizmetini kullanmaya başlamak kolay ve ekonomik bir işlemdir. 30 günlük ücretsiz deneme sürümü, hizmetin neler yapabileceğini keşfetmenizi ve uygulamanızın ihtiyaçlarına uygun olup olmadığına karar vermenizi sağlar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 46ada91060aa095b7c041ff75abb6256f3d05853
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464689"
---
# <a name="try-speech-services-for-free"></a>Konuşma hizmetlerini ücretsiz deneyin

Konuşma hizmetlerini kullanmaya başlamak kolay ve ekonomik bir işlemdir. 30 günlük ücretsiz deneme sürümü, hizmetin neler yapabileceğini keşfetmenizi ve uygulamanızın ihtiyaçlarına uygun olup olmadığına karar vermenizi sağlar.

Daha fazla zaman gerekiyorsa, bir Microsoft Azure hesabına kaydolun — hizmet kredisi, 30 güne kadar ücretli bir konuşma Hizmetleri aboneliğine uygulayabileceğiniz $200 ile gelir.

Son olarak, konuşma Hizmetleri, uygulama geliştirmeye uygun olan ücretsiz ve düşük hacimli bir katman sunar. Hizmet kredinizin süresi dolduktan sonra bile bu ücretsiz aboneliği tutabilirsiniz.

## <a name="free-trial"></a>Ücretsiz deneme sürümü

30 günlük ücretsiz deneme sürümü, standart fiyatlandırma katmanına sınırlı bir süre için erişmenizi sağlar.

30 günlük ücretsiz denemeye kaydolmak için:

1. Bilişsel [Hizmetler 'ı deneyin](https://azure.microsoft.com/try/cognitive-services/)' e gidin.

1. **Konuşma API 'leri** sekmesini seçin.

   ![Konuşma API 'SI-konuşma Hizmetleri sekmesi](media/index/cognitive-services-speech-api-tab.png)

1. **Konuşma Hizmetleri**altında, **API anahtarı al**' ı seçin.

   ![Konuşma API 'SI-API anahtarını al](media/index/speech-api-get-api-key.png)

1. Koşulları kabul edin ve açılır menüden yerel ayarınızı seçin.

   ![Konuşma API 'SI-koşulları kabul etme](media/index/speech-api-agree-to-terms.png)

1. Microsoft, Facebook, LinkedIn veya GitHub hesabınızı kullanarak oturum açın.

    [Microsoft hesabı portalında](https://account.microsoft.com/account)ücretsiz Microsoft hesabı kaydolabilirsiniz. Başlamak için **Microsoft hesabıyla oturum açın** ' yi seçin ve ardından oturum açmanız istendiğinde oluştur ' u seçin **.** Yeni Microsoft hesabı oluşturup doğrulamak için adımları izleyin.

Bilişsel hizmetler 'i denemek için oturum açtıktan sonra, ücretsiz denemeniz başlar. Görüntülenen Web sayfası, şu anda deneme aboneliğine sahip olduğunuz tüm Azure bilişsel hizmetler hizmetlerini listeler. İki abonelik anahtarı **konuşma Hizmetleri**' nin yanında listelenmiştir. Uygulamalarınızda her iki anahtarı da kullanabilirsiniz.

> [!NOTE]
> Tüm ücretsiz deneme abonelikleri Batı ABD bölgesidir. İstek yaptığınızda `westus` uç noktasını kullandığınızdan emin olun.

## <a name="new-azure-account"></a>Yeni Azure hesabı

Yeni Azure hesapları, 30 güne kadar kullanılabilir bir $200 hizmet kredisi alır. Bu kredisi, konuşma hizmetlerini daha ayrıntılı incelemek veya uygulama geliştirmeye başlamak için kullanabilirsiniz.

Yeni bir Azure hesabına kaydolmak için [Azure kaydolma sayfasına](https://azure.microsoft.com/free/ai/)gidin, **ücretsiz Başlat ' ı** seçin ve Microsoft hesabı kullanarak yeni bir Azure hesabı oluşturun.

[Microsoft hesabı portalında](https://account.microsoft.com/account)ücretsiz Microsoft hesabı kaydolabilirsiniz. Başlamak için **Microsoft hesabıyla oturum açın** ' yi seçin ve ardından oturum açmanız istendiğinde oluştur ' u seçin **.** Yeni Microsoft hesabı oluşturup doğrulamak için adımları izleyin.

Azure hesabınızı oluşturduktan sonra, konuşma Hizmetleri için bir abonelik başlatmak üzere sonraki bölümdeki adımları izleyin.

## <a name="create-a-speech-resource-in-azure"></a>Azure 'da bir konuşma kaynağı oluşturma

Azure hesabınıza bir konuşma Hizmetleri kaynağı (ücretsiz veya ücretli katman) eklemek için:

1. Microsoft hesabı kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.

1. Portalın sol üst kısmında **kaynak oluştur** ' u seçin.

    ![Konuşma API 'SI-kaynak oluşturma](media/index/speech-api-create-resource.png)

1. **Yeni** pencerede **konuşma**araması yapın.

1. Arama sonuçlarında **konuşma**' yı seçin.

    ![Konuşma API 'SI-konuşma seçin](media/index/speech-api-select-speech.png)

1. **Konuşma**altında **Oluştur** düğmesini seçin.

    ![Konuşma API 'SI-oluştur düğmesi](media/index/speech-api-create-button.png)

1. **Oluştur**altında şunu girin:

   * Yeni kaynak için bir ad. Ad, aynı hizmete yönelik birden çok abonelik arasında ayrım yapmanıza yardımcı olur.
   * Ücretlerin nasıl faturalandırıldığını belirlemek için yeni kaynağın ilişkilendirildiği Azure aboneliğini seçin.
   * Kaynağın kullanılacağı [bölgeyi](regions.md) seçin.
   * Ücretsiz veya ücretli bir fiyatlandırma katmanı seçin. Her bir katmanın fiyatlandırma ve kullanım kotalarıyla ilgili tüm bilgiler için **tam fiyatlandırma ayrıntılarını görüntüle** ' yi seçin.
   * Bu konuşma aboneliği için yeni bir kaynak grubu oluşturun veya aboneliği var olan bir kaynak grubuna atayın. Kaynak grupları, çeşitli Azure aboneliklerinizi düzenli halde tutmanıza yardımcı olur.
   * Gelecekte aboneliğinize daha kolay erişim için **panoya sabitle** onay kutusunu seçin.
   * Oluştur ' u seçin **.**

     ![Konuşma API 'SI-oluştur Seç](media/index/speech-api-select-create.png)

     Yeni konuşma kaynağınızın oluşturulması ve dağıtılması biraz zaman alır. Yeni kaynağınız hakkındaki bilgileri görmek için **hızlı başlangıç** ' ı seçin.

     ![Konuşma API 'SI-kaynağı dağıtma](media/index/speech-api-deploy-resource.png)

1. **Hızlı başlangıç**altında, abonelik anahtarlarınızı göstermek için adım 1 ' ın altındaki **anahtarlar** bağlantısını seçin. Her abonelik iki anahtara sahiptir; uygulamanızda herhangi bir anahtarı kullanabilirsiniz. Kodunuza yapıştırmak üzere panoya kopyalamak için her bir anahtarın yanındaki düğmeyi seçin.

> [!NOTE]
> Bir veya birden çok bölgede sınırsız sayıda standart katmanlı abonelik oluşturabilirsiniz. Ancak, yalnızca bir serbest katmanlı abonelik oluşturabilirsiniz. Ücretsiz katmanda 7 gün boyunca kullanılmamış olarak kalan model dağıtımları otomatik olarak yeniden kullanıma sunulacaktır.

## <a name="switch-to-a-new-subscription"></a>Yeni bir aboneliğe geç

Bir abonelikten diğerine geçiş yapmak için, örneğin ücretsiz deneme süreniz sona erdiğinde veya uygulamanızı yayımladığınızda, kodunuzdaki bölge ve abonelik anahtarını yeni Azure kaynağının bölge ve abonelik anahtarıyla değiştirin.

> [!NOTE]
> Batı ABD (`westus`) bölgesinde ücretsiz deneme anahtarları oluşturulur. İsterseniz, Azure panosu aracılığıyla oluşturulan bir abonelik başka bir bölgede olabilir.

* Uygulamanız bir [konuşma SDK 'sı](speech-sdk.md)kullanıyorsa, bir konuşma yapılandırması oluştururken `westus`gibi bölge kodunu sağlarsınız.
* Uygulamanız, konuşma Hizmetleri 'nin [REST API 'lerinden](rest-apis.md)birini kullanıyorsa bölge, istek yaparken kullandığınız uç nokta URI 'sinin bir parçasıdır.

Bölge için oluşturulan anahtarlar yalnızca o bölgede geçerlidir. Bunları diğer bölgelerle birlikte kullanmaya çalışmak, kimlik doğrulama hatalarına neden olur.

## <a name="next-steps"></a>Sonraki adımlar

10 dakikalık hızlı başlangıçlardan birini tamamlayıp SDK örneklerimize göz atın:

> [!div class="nextstepaction"]
> [Hızlı başlangıç: C# ](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [konuşma SDK örneklerinde](speech-sdk.md#get-the-samples) konuşmayı tanıma

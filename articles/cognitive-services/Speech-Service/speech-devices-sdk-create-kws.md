---
title: Özel anahtar sözcük okuma hizmeti oluşturma
titleSuffix: Azure Cognitive Services
description: Cihazınız her zaman bir anahtar sözcüğü (veya tümceciği) dinler. Kullanıcı anahtar sözcüğünü söyyorsa, cihaz sonraki tüm sesleri buluta gönderir ve Kullanıcı konuşmayı durduruncaya kadar. Anahtar keliinizi özelleştirmek, cihazınızı ayırt etmenin ve markanızı güçlendirmenin etkili bir yoludur.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bf9afb66163532b4095e0d30b1167010320abbf8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490951"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Konuşma hizmetini kullanarak özel anahtar sözcük oluşturma

Cihazınız her zaman bir anahtar sözcüğü (veya tümceciği) dinler. Örneğin, "Hey Cortana", Cortana yardımcısı için bir anahtar sözcüktür. Kullanıcı anahtar sözcüğünü söyyorsa, cihaz sonraki tüm sesleri buluta gönderir ve Kullanıcı konuşmayı durduruncaya kadar. Anahtar keliinizi özelleştirmek, cihazınızı ayırt etmenin ve markanızı güçlendirmenin etkili bir yoludur.

Bu makalede, cihazınız için özel bir anahtar sözcük oluşturmayı öğreneceksiniz.

## <a name="choose-an-effective-keyword"></a>Etkin bir anahtar sözcük seçin

Bir anahtar sözcük seçerken aşağıdaki yönergeleri göz önünde bulundurun:

* Anahtar kelimelisiniz Ingilizce bir sözcük veya tümcecik olmalıdır. İki saniyeden daha uzun sürmemelidir.

* 4 ile 7 hece hecelere en iyi şekilde çalışan sözcükler. Örneğin, "Hey, Computer" iyi bir anahtar sözcüktür. Yalnızca "Hey" kötü bir alışkanlıktır.

* Anahtar sözcükler, yaygın Ingilizce Söyleniş kurallarını izlemelidir.

* Yaygın Ingilizce telaffuz kuralları takip eden, benzersiz veya hatta oluşturulmuş bir sözcük, hatalı pozitif sonuçları düşürebilir. Örneğin, "computerama" iyi bir anahtar sözcük olabilir.

* Ortak bir sözcük seçemezsiniz. Örneğin, "yemek" ve "Go", insanların normal konuşmada sıkça söyledikleri sözcüklerdir. Cihazınız için yanlış Tetikleyiciler olabilir.

* Alternatif söylenişleri olabilecek bir anahtar sözcük kullanmaktan kaçının. Kullanıcıların, cihazlarını yanıt vermesini sağlamak için "sağ" söylenişini bilmeleri gerekir. Örneğin, "509", "5 0 9", "5 0 9" veya "509" olarak kullanılabilir. "R.E.I." "r-e-i" veya "Ray" olarak kullanılabilir. "Canlı", "/līv/" veya "/Liv/" ile yapılabilir.

* Özel karakterler, semboller veya basamaklar kullanmayın. Örneğin, "Go #" ve "20 + kediler" iyi anahtar kelimelerdir. Bununla birlikte, "keskin" veya "yirmi Plus kediler" işe devam edebilir. Markanızdaki sembolleri kullanmaya devam edebilir ve uygun telaffuz zorlamak için pazarlama ve belgeleri kullanabilirsiniz.

> [!NOTE]
> Anahtar kelime olarak bir trademarked sözcüğü seçerseniz, bu ticari marka sahibi olduğunuzdan veya ticari marka sahibinden, kelimeyi kullanmak için izninizin olduğundan emin olun. Microsoft, sizin tercih ettiğiniz anahtar kelimeden çıkabilecek herhangi bir yasal sorun için tabi değildir.

## <a name="create-your-keyword"></a>Anahtar keliinizi oluşturma

Aygıtınızla özel bir anahtar sözcük kullanabilmeniz için, Microsoft özel anahtar sözcük oluşturma hizmeti ile bir anahtar sözcük oluşturmanız gerekir. Bir anahtar sözcük belirledikten sonra, hizmet cihazınızda anahtar sözcüğü etkinleştirmek için geliştirme setinizi dağıttığınız bir dosya oluşturur.

1. [Konuşma Studio](https://aka.ms/sdsdk-speechportal) 'ya gidin ve **oturum açın** veya henüz bir konuşma aboneliğiniz yoksa, [**abonelik oluştur**](https://go.microsoft.com/fwlink/?linkid=2086754)' u seçin.

1. [Özel anahtar sözcük](https://aka.ms/sdsdk-wakewordportal) sayfasında, istediğiniz anahtar sözcüğü yazın ve **anahtar sözcük ekle**' ye tıklayın. Etkin bir anahtar sözcük seçmenize yardımcı olmak için bazı [kılavuzlardır](#choose-an-effective-keyword) . Destek şu anda en-US diliyle sınırlıdır.

    ![Anahtar keliinizi girin](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Portal şimdi anahtar kelimeden aday söylenişleri oluşturacak. Oynat düğmelerine tıklayarak her bir adayı dinleyin ve yanlış olan herhangi bir söylenin yanındaki denetimleri kaldırın. Yalnızca güzel Söylenişler denetlendikten sonra, anahtar sözcüğünü oluşturmaya başlamak için **Gönder** ' i seçin. Anahtar sözcüğünü değiştirmek istiyorsanız, ilk olarak var olanı, üzerine gelindiğinde satırın sağ tarafında görüntülenen Sil düğmesine tıklayarak kaldırın.

    ![Anahtar keliinizi gözden geçirin](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Modelin oluşturulması bir dakika kadar sürebilir. Bundan sonra dosyayı indirmeniz istenir.

    ![Anahtar keliinizi indirin](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. . Zip dosyasını bilgisayarınıza kaydedin. Özel anahtar sözcüðünü geliştirme paketine dağıtmak için bu dosyaya ihtiyacınız olacaktır.

## <a name="next-steps"></a>Sonraki adımlar

[Konuşma CIHAZLARı SDK hızlı başlangıç](https://aka.ms/sdsdk-quickstart)ile özel anahtar keliinizi test edin.

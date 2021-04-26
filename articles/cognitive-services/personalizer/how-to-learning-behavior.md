---
title: Öğrenme davranışını yapılandırma
description: Apprence modu, kişiselleştirici hizmeti ve makine öğrenimi özellikleri sayesinde size güven sağlar ve çevrimiçi trafiğe karşı, hizmetin öğrenildiği bilgileri (' den öğrenilebilir olan ölçümleri) sağlar.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94579300"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Kişiselleştirici öğrenme davranışını yapılandırma

[Apprence modu](concept-apprentice-mode.md) , kişiselleştirici hizmeti ve makine öğrenimi özellikleri ile güven ve güvenilirlik sağlar ve çevrimiçi trafiğe karşı, hizmetin öğrenildiği bilgilerin gönderildiği güvencesi sağlar.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Apprenlet modunu yapılandırma

1. Kişiselleştirici kaynağınız için [Azure Portal](https://portal.azure.com)oturum açın.

1. **Yapılandırma** sayfasında, **öğrenme davranışı** sekmesinde, ana **hat eylemi Döndür ' ü seçin, bir apprenlet olarak öğrenin ve** ardından **Kaydet**' i seçin.

> [!div class="mx-imgBorder"]
> ![Azure portal 'de apprence modu öğrenme davranışını yapılandırma ekran görüntüsü](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Mevcut uygulamada yapılan değişiklikler

Mevcut uygulamanız, görüntüleme işlemlerini nasıl seçmekte veya uygulamanın değeri, bu eylemin **yeniden** nasıl belirlediğini değiştirmemelidir. Uygulamaya yapılan tek değişiklik, Kişiselleştiriciye ait derecelendirme API 'sine gönderilen eylemlerin sırası olabilir. Uygulamanızın Şu anda görüntülediği eylem, eylem listesindeki _ilk eylem_ olarak gönderilir. [Derecelendirme API 'si](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) , kişiselleştirici modelinizi eğitmek için bu ilk eylemi kullanır.

### <a name="configure-your-application-to-call-the-rank-api"></a>Uygulamanızı, derecelendirme API 'sini çağıracak şekilde yapılandırma

Uygulamanıza kişiselleştirici eklemek için, derecelendirme ve yeniden dengeleme API 'Lerini çağırmanız gerekir.

1. Mevcut uygulama mantığınızdaki noktadan sonra, eylem ve bunların özelliklerinin listesini belirlediğiniz noktadan sonra [derece API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) çağrısını ekleyin. Eylemler listesindeki ilk eylemin, mevcut mantığınızın seçtiği eylem olması gerekir.

1. Sıralama API 'SI yanıtının **Reward eylem kimliğiyle** ilişkili eylemi görüntüleyecek şekilde kodunuzu yapılandırın.

### <a name="configure-your-application-to-call-reward-api"></a>Uygulamanızı, Reward API çağrısı için yapılandırma

1. Mevcut iş mantığınızı kullanarak, görüntülenmiş eylemin **yeniden** nasıl hesaplanacağını hesaplayabilirsiniz. Değerin 0-100 aralığında olması gerekir. [Ödül API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)kullanarak bu yeniden kişiselleştiriciye gönderin. Ödül değeri hemen beklenmez ve iş mantığınıza bağlı olarak bir zaman diliminde geciktirilebilir.

1. Yapılandırılan **yeniden bekleme süresi** içinde geri dönmezseniz, bunun yerine varsayılan geri dönme kullanılacaktır.

## <a name="evaluate-apprentice-mode"></a>Apprenlet modunu değerlendir

Azure portal, kişiselleştirici kaynağınız için **değerlendirmeler** sayfasında, **geçerli öğrenme davranışı performansını** gözden geçirin.

> [!div class="mx-imgBorder"]
> ![Azure portal 'de apprence modu öğrenme davranışının değerlendirilmesine yönelik ekran görüntüsü](media/settings/evaluate-apprentice-mode.png)

Apprenlet modu aşağıdaki **değerlendirme ölçümlerini** sağlar:
* **Taban çizgisi – ortalama ödül**: uygulamanın varsayılan (taban çizgisi) ortalama yeniden sürümleri.
* **Kişiselleştirici – ortalama ödül**: Toplam Rede kişiselleştiriciye ait ortalama, potansiyel olarak gelmiş olabilir.
* **En son 1000 olay sayısı için başarı oranı**: en son 1000 olay üzerinden normalleştirilmeli ve kişiselleştirici bir şekilde değerlendiriliyor.

## <a name="switch-behavior-to-online-mode"></a>Davranışı çevrimiçi moda geçir

Kişiselleştirmeye yönelik bir ortalama% 75-85 yuvarlanan ortalama ile eğitilen, model çevrimiçi moda geçmeye hazırlanıyor.

Kişiselleştirici kaynağınız için Azure portal, **yapılandırma** sayfasında, **öğrenme davranışı** sekmesinde **en iyi eylemi Döndür** ' ü seçin ve ardından **Kaydet**' i seçin.

Derecelendirme ve yeniden iletme API çağrılarında herhangi bir değişiklik yapmanız gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

* [Model ve öğrenme ayarlarını yönetme](how-to-manage-model.md)

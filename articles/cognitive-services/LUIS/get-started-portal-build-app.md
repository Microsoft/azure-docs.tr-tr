---
title: 'Hızlı başlangıç: LUO portalında yeni bir uygulama oluşturma'
description: Bu hızlı başlangıçta, bir uygulamanın, amaçlarınızın ve varlıkların temel parçalarını ve Ayrıca Lua portalındaki örnek utbotlarla test oluşturursunuz.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: c2f2bafe68dd3f3dad704ba12cdd4d01bf4f6588
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278771"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Hızlı başlangıç: LUO portalında yeni bir uygulama oluşturma

Bu hızlı başlangıçta, LUı portalında yeni bir uygulama oluşturacaksınız. İlk olarak, uygulama, **Amaç** ve **varlıkların** temel parçalarını oluşturun. Ardından, tahmin edilen amacı almak için etkileşimli test paneline örnek bir Kullanıcı yazarak uygulamayı test edin.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Uygulama oluşturun

Bir uygulama oluşturmak için **+ Yeni uygulama**' yı seçin. 

Görüntülenen pencerede, aşağıdaki bilgileri girin:

|Ad  |Açıklama  |
|---------|---------|
|Ad     | Uygulamanız için bir ad. Örneğin, "ana Otomasyon". Gereklidir.        |
|Kültür     | Uygulamanızın anladığı ve kulakça dili. Gereklidir.   |
|Description | Uygulamanız için bir açıklama. İsteğe bağlı.
|Tahmin kaynağı | Sorgu alacak tahmin kaynağı. İsteğe bağlı. |

**Bitti** seçeneğini belirleyin.

>[!NOTE]
>Uygulama oluşturduktan sonra kültür değiştirilemez.


## <a name="create-intents"></a>Amaç oluşturma

LUIN uygulaması oluşturulduktan sonra, amaçları oluşturmanız gerekir. Amaçlar, kullanıcılara metin sınıflandırın bir yoludur. Örneğin, insan kaynakları uygulamasında iki işlev olabilir. Kişilere yardım etmek için:

 1. İşler için bul ve Uygula
 1. İşler için uygulanacak formları bulma

Uygulamanın _iki farklı amacı_ aşağıdaki amaçlar için hizalanacaktır:

|Amaç|Kullanıcıdan örnek metin<br>_söylenişi_ olarak bilinen|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Amaç oluşturmak için aşağıdaki adımları izleyin:

1. Uygulama oluşturulduktan sonra, **derleme** bölümünün **amaçlar** sayfasında olduğunuzdan emin olun. **Oluştur**’u seçin.

   [![Yeni amaç oluşturmak için ' oluştur ' seçeneğinin ekran görüntüsü](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Amaç adını girip `FindForm` **bitti**' yi seçin.

## <a name="add-an-example-utterance"></a>Örnek bir mekan ekleyin

Amaçları oluşturduktan sonra örnek bir parametre ekleyin. Örnek, kullanıcının bir sohbet bot veya diğer istemci uygulamasına girdiği metindir. Kullanıcı metninin amacı bir LUO amacı ile eşlenir. Bu örnek uygulamanın amacı için `FindForm` , örnek olarak form numarasını içerecektir. İstemci uygulaması, kullanıcının isteğini yerine getirmek için form numarasına ihtiyaç duyuyor, bu yüzden bunu utterde eklemek önemlidir.

İçin **amaçlar** sayfasında, `FindForm` **örnek utterance** altına aşağıdaki örnek söyleylerini ekleyin, 

|#|Örnek konuşmalar|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Bu örnek, tasarıma göre aşağıdaki yollarla farklılık gösterir:

* söylenişi uzunluğu
* [edilen](luis-reference-application-settings.md#punctuation-normalization)
* sözcük seçimi
* fiil zaman hali (yani, was, olacaktır)
* sözcük sırası


## <a name="create-a-regular-expression-entity"></a>Normal ifade varlığı oluşturma

Çalışma zamanı tahmin yanıtında form numarasını döndürmek için, form numarası bir varlık olarak ayıklanmalıdır. Form numarası metni yüksek yapılandırılmış olduğundan, normal ifade varlığı kullanabilirsiniz. Normal ifade varlığını aşağıdaki adımlarla oluşturun:

1. Sol taraftaki menüden **varlıklar** ' ı seçin.

1. **Varlıklar** sayfasında **Oluştur** ' u seçin.

1. Adı girin `FormNumber` , **Regex** varlık türünü seçin.

1. Normal ifadeyi, `hrf-[0-9]{6}` **Regex** alanına girin. Bu giriş, değişmez karakterlerle eşleşir, `hrf-` tam olarak altı basamak sağlar ve ardından **Oluştur**' u seçin.

    > [!div class="mx-imgBorder"]
    > ![Normal ifade varlığı oluşturma ekran görüntüsü](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Bu varlık, herhangi bir amaç içindeki normal ifadeyle eşleşen tüm metinleri ayıklar.

## <a name="add-example-utterances-to-the-none-intent"></a>Hiçbiri amacına örnek ekleme

**Hiçbiri** amacı geri dönüş amacını ve boş bırakılmalıdır. Bu amaç, uygulamanın diğer amaçları için eklemiş olduğunuz her 10 örnek için bir söylenişi içermelidir.

**Hiçbir** üyenin örnek utbotları, istemci uygulama etki alanınız dışında olmalıdır.

1. Sol menüden **amaçlar** ' ı seçin ve ardından amaçlar listesinden **hiçbiri** ' ni seçin.

1. Amacına aşağıdaki örnek söyleyeni ekleyin:

   |Hiçbiri amacı örnek söyliği|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   Bu uygulama için bu örnek, etki alanının dışındadır. Etki alanınız hayvanlar veya okyanus içeriyorsa, **none** amacı için farklı örnek utbotları kullanmanız gerekir.

## <a name="train-the-app"></a>Uygulamayı eğitme

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Örnek mekan normal ifade varlığına bakın

1. Sol menüden **amaçlar** ' a tıklayarak varlığın **FindForm** hedefi ' nde bulunduğunu doğrulayın. Ardından **FindForm** hedefini seçin.

   Varlık, örnek noktada göründüğü yerde işaretlenir.

   > [!div class="mx-imgBorder"]
   > [![Varlıklarla işaretlenmiş tüm örnek dıklarından oluşan ekran görüntüsü](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Etkileşimli test bölmesiyle yeni uygulamanızı test etme

Varlığın, uygulamanın henüz görülmemiş yeni dıklardan ayıklandığını doğrulamak için, lug portalındaki etkileşimli **Test** bölmesini kullanın.

1. Sağ üst menüden **Test** ' i seçin.

1. Yeni bir söylenişi ekleyin ve ardından ENTER tuşuna basın:

   ```Is there a form named hrf-234098```

    Varlık tahminlerini görmek için **İnceleme** ' yi seçin.

   > [!div class="mx-imgBorder"]
   > ![Test bölmesinde yeni söylenişi test etme ekran görüntüsü](./media/get-started-portal-build-app/test-new-utterance.png)

   En iyi tahmin edilen amaç, %90 güvenilirlikli (0,977) ile doğru **FindForm** . **Formnumber** varlığı HRF-234098 değeri ile ayıklanır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç ile işiniz bittiğinde ve sonraki hızlı başlangıca geçmeden, üst gezinti menüsünden **uygulamalarım** ' ı seçin. Ardından listedeki uygulamanın sol onay kutusunu seçin ve listenin üzerindeki bağlam araç çubuğundan  **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulamayı dağıtma](get-started-portal-deploy-app.md)

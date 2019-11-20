---
title: 'Hızlı başlangıç: özel bir komut (Önizleme) oluşturma-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Bu makalede barındırılan özel komutlar uygulaması oluşturup test edersiniz.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 80eb0a2018ece23de80e8eb9c4a68c149b590440
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111375"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Hızlı başlangıç: özel komut oluşturma (Önizleme)

Bu makalede, barındırılan özel komutlar uygulaması oluşturmayı ve test yapmayı öğreneceksiniz.
Uygulama "TV 'yi aç" gibi bir söylik algılar ve "Tamam, TV 'yi açarak" daha basit bir iletiyle yanıt verir.

## <a name="prerequisites"></a>Önkoşullar

- Bir konuşma aboneliği. [Konuşma hizmetini ücretsiz deneyin](~/articles/cognitive-services/speech-service/get-started.md).

  > [!NOTE]
  > Önizleme süresince, abonelik anahtarları için yalnızca westus2 bölgesi desteklenir.

- [Language Understanding](https://www.luis.ai/home) (lusıs) yazma anahtarı:
  1. Bir Web tarayıcısı açın ve [Azure Portal](https://portal.azure.com) gidin
  1. Kaynak oluştur ' u seçin
  1. Arayın ve [Language Understanding](https://aka.ms/sc-luis-all) seçin
  1. Oluşturma seçeneklerinde yazma seçin
  1. Kaynak dağıtıldıktan sonra kaynağa gidin ve anahtarı hızlı başlangıç veya anahtarlar bölümünden kopyalayın

      > [!div class="mx-imgBorder"]
      > ![yazma kaynağı oluşturma](media/custom-speech-commands/resources-lu-authoring.png)

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Özel komutlar için konuşma Studio 'ya gidin

1. Web tarayıcınızı açın ve [konuşma Studio](https://speech.microsoft.com/) 'ya gidin
1. Portalda oturum açmak için kimlik bilgilerinizi girin

   - Varsayılan görünüm, konuşma abonelikleri listesidir
     > [!NOTE]
     > Abonelik Seç sayfasını görmüyorsanız, üstteki çubukta ayarlar menüsünden "konuşma kaynakları" ' nı seçerek buraya gidebilirsiniz.

1. Konuşma aboneliğinizi seçin ve ardından **Studio 'Ya git** ' i seçin.
1. **Özel komutları seçin (Önizleme)**

Varsayılan görünüm, oluşturduğunuz özel komutlar uygulamalarının bir listesidir.

## <a name="create-a-custom-commands-project"></a>Özel komutlar projesi oluşturma

1. Yeni proje oluşturmak için **Yeni proje** ' yi seçin

   > [!div class="mx-imgBorder"]
   > Yeni proje oluşturmak ![](media/custom-speech-commands/create-new-project.png)

1. Proje adını ve dilini girip devam etmek için **İleri** 'yi seçin
1. LUSıS yazma anahtarınızı girin
1. Oluşturulduktan sonra projenizi seçin

Artık görünümlerinizin özel komutlar uygulamanıza genel bir bakış olması gerekir.

## <a name="create-a-new-command"></a>Yeni bir komut oluştur

Artık bir komut oluşturabilirsiniz. Tek bir utterance, `turn on the tv`ve `Ok, turning on the TV`ileti ile yanıt verecek bir örnek kullanalım.

1. Komutlar ' ın yanındaki `+` simgesini seçerek yeni bir komut oluşturun ve adı verin `TurnOn`
1. **Kaydet**’i seçin

> [!div class="mx-imgBorder"]
> ![komut](media/custom-speech-commands/create-add-command.png) oluşturma

Bir komut bir kümesidir:

| Grup            | Açıklama                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Örnek Cümleler | Kullanıcının bu komutu tetikleyebilmesi için söyledikleri örnek                                                                 |
| Parametreler       | Komutu tamamlaması için gereken bilgiler                                                                                |
| Tamamlama kuralları | Komutu yerine getirmek için gerçekleştirilecek eylemler. Örneğin, kullanıcıya yanıt vermek veya başka bir Web hizmetiyle iletişim kurmak için |
| Gelişmiş kurallar   | Daha belirgin veya karmaşık durumları işlemek için ek kurallar                                                              |

### <a name="add-a-sample-sentence"></a>Örnek tümce ekleme

Örnek cümleler ile başlayalım ve kullanıcının şunları söyleyebileceğiniz bir örnek sunalım:

```
turn on the tv
```

Şimdilik, tamamlanma kurallarına taşıyabilmemiz için parametresiz bir parametre yoktu.

### <a name="add-a-completion-rule"></a>Tamamlama kuralı ekleme

Şimdi bir eylemin gerçekleştiğini belirten kullanıcıya yanıt vermek için bir tamamlama kuralı ekleyin.

> [!div class="mx-imgBorder"]
> ![tamamlama kuralı oluşturma](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Ayar    | Önerilen değer                        | Açıklama                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Kural Adı  | "ConfirmationResponse"                 | Kuralın amacını açıklayan bir ad          |
| Koşullar | None                                   | Kuralın ne zaman çalıştırılabilmesine belirleme koşulları    |
| Eylemler    | SpeechResponse "Tamam, TV 'yi açma" | Kural koşulu true olduğunda gerçekleştirilecek eylem |

## <a name="try-it-out"></a>Deneyin

Test sohbeti panelini kullanarak davranışı test edin.

> [!div class="mx-imgBorder"]
> Webchat ile test ![](media/custom-speech-commands/create-basic-test-chat.png)

- Şunu yazın: "TV 'yi açma"
- Beklenen yanıt: "Tamam, TV 'yi açma"

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Hızlı başlangıç: parametrelerle özel komut oluşturma (Önizleme)](./quickstart-custom-speech-commands-create-parameters.md)

---
title: 'Hızlı başlangıç: özel komutlar kullanarak bir ses Yardımcısı oluşturma'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, konuşma Studio 'Yu kullanarak temel bir özel komut uygulaması oluşturur ve test edersiniz.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: e046f8cbf6fa0418244f20e9a0c6f75f6da34136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434636"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Özel Komutlar kullanarak ses yardımcısı oluşturma

Bu hızlı başlangıçta, konuşma Studio 'Yu kullanarak temel bir özel komut uygulaması oluşturur ve test edersiniz. Ayrıca, bu uygulamaya bir Windows istemci uygulamasından de erişebileceksiniz.

## <a name="region-availability"></a>Bölge kullanılabilirliği
Şu anda, özel komutlar şu bölgelerde oluşturulan konuşma aboneliklerini destekler:
* Batı ABD
* Batı ABD 2
* Doğu ABD
* Doğu ABD 2
* Orta Batı ABD
* Kuzey Avrupa
* West Europe
* Doğu Asya
* Güneydoğu Asya
* Orta Hindistan

## <a name="prerequisites"></a>Önkoşullar

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Özel komutları destekleyen bir bölgede Azure konuşma kaynağı oluşturun.</a> Desteklenen bölgelerin listesi için yukarıdaki **bölge kullanılabilirliği** bölümüne bakın.
> * Örnek [Akıllı Oda Lite](https://aka.ms/speech/cc-quickstart) json dosyasını indirin.
> * [Windows Voice Yardımcısı istemcisinin](https://aka.ms/speech/va-samples-wvac)en son sürümünü indirin.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Özel komutlar için konuşma Studio 'ya gidin

1. Bir Web tarayıcısında, [konuşma Studio](https://speech.microsoft.com/)'ya gidin.
1. Portalda oturum açmak için kimlik bilgilerinizi girin.

   Varsayılan görünüm, konuşma abonelikleri listesidir.
   > [!NOTE]
   > Abonelik Seç sayfasını görmüyorsanız, üstteki çubukta ayarlar menüsünden "konuşma kaynakları" ' nı seçerek buraya gidebilirsiniz.

1. Konuşma aboneliğinizi seçin ve ardından **Studio 'Ya git**' i seçin.
1. **Özel komutlar**' ı seçin.

   Varsayılan görünüm, seçtiğiniz aboneliğin altında sahip olduğunuz özel komutlar uygulamalarının bir listesidir.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Mevcut bir uygulamayı yeni bir özel komutlar projesi olarak içeri aktar

1. Proje oluşturmak için **Yeni proje** ' yi seçin.

1. **Ad** kutusuna proje adını `Smart-Room-Lite` (veya istediğiniz bir şeyi) girin.
1. **Dil** listesinde **İngilizce (Birleşik Devletler)** seçeneğini belirleyin.
1. **Dosyaları araştır** ' ı seçin ve tarama penceresinde, dosyadaki **SmartRoomLite.js** seçin.

    > [!div class="mx-imgBorder"]
    > ![Proje oluşturma](media/custom-commands/import-project.png)

1.  **Lusıs yazma kaynak** listesinde bir yazma kaynağı seçin. Geçerli bir yazma kaynağı yoksa,  **yenı lusıs Authoring Resource oluştur**' u seçerek bir tane oluşturun.

    > [!div class="mx-imgBorder"]
    > ![Kaynak oluşturma](media/custom-commands/create-new-luis-resource.png)
    
    
    1. **Kaynak adı** kutusuna kaynağın adını girin.
    1. **Kaynak grubu** listesinde, bir kaynak grubu seçin.
    1. **Konum** listesinde bir konum seçin.
    1. **Fiyatlandırma katmanı** listesinde bir katman seçin.
    
    
    > [!NOTE]
    > "Kaynak grubu" alanına istenen kaynak grubu adını girerek kaynak grupları oluşturabilirsiniz. Kaynak grubu, **Oluştur** seçildiğinde oluşturulur.


1. Ardından, projenizi oluşturmak için **Oluştur** ' u seçin.
1. Proje oluşturulduktan sonra projenizi seçin.
Artık yeni özel komutlar uygulamanıza genel bakış görmeniz gerekir.

## <a name="try-out-some-voice-commands"></a>Bazı sesli komutları deneyin
1. Sağ bölmenin en üstünde **eğitme** ' yi seçin.
1. Eğitim tamamlandığında, **Test** ' i seçin ve aşağıdaki söyleylerini deneyin:
    - TV 'yi açın
    - Sıcaklığın 80 derece olarak ayarlanması
    - Kapat
    - TV
    - 5 PM için bir alarm ayarla

## <a name="integrate-custom-commands-application-in-an-assistant"></a>Özel komutlar uygulamasını bir yardımcı içinde tümleştirme
Bu uygulamaya, konuşma Studio 'Nun dışından erişebilmek için, uygulamayı yayımlamanız gerekir. Bir uygulamayı yayımlamak için, tahmin LUO kaynağını yapılandırmanız gerekecektir.  

### <a name="update-prediction-luis-resource"></a>Tahmin LUSıS kaynağını güncelleştir


1. Sol bölmedeki **Ayarlar** ' ı seçin ve orta bölmedeki  **lusıs kaynakları** ' nı seçin.
1. Bir tahmin kaynağı seçin veya **Yeni kaynak oluştur**' u seçerek bir tane oluşturun.
1. **Kaydet**’i seçin.
    
    > [!div class="mx-imgBorder"]
    > ![LUSıS kaynaklarını ayarlama](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Yazma kaynağı ayda yalnızca 1.000 tahmin uç nokta isteklerini desteklediğinden, özel komutlar uygulamanızı yayımlamadan önce mandatorily bir LUSıS tahmini kaynağı ayarlamanız gerekir.

### <a name="publish-the-application"></a>Uygulamayı yayımlama

Sağ bölmenin en üstünde  **Yayımla** ' yı seçin. Yayımlama tamamlandığında yeni bir pencere görüntülenir. **Uygulama kimliği** ve **konuşma kaynak anahtarı** değerini buradan aklınızda edin. Bu iki değere sahip olmanız gerekir ve bu iki değer, uygulamaya konuşma Studio dışından erişebilir.

Alternatif olarak, **Ayarlar**  >  **genel** bölümünü seçerek de bu değerleri alabilirsiniz.

### <a name="access-application-from-client"></a>İstemciden uygulamaya erişim

Bu makalenin kapsamında, önkoşulların bir parçası olarak indirdiğiniz Windows Voice Yardımcısı istemcisini kullanacağız. Klasörün sıkıştırmasını açın.
1. **VoiceAssistantClient.exe** başlatın.
1. Yeni bir yayımlama profili oluşturun ve **bağlantı profili** için değer girin. **Genel ayarlar** bölümünde değerler **abonelik anahtarı** (Bu, uygulamayı yayımlarken kaydettiğiniz **konuşma kaynak anahtarı** değeri ile aynıdır), **ABONELIK anahtar bölgesi** ve **özel komutlar uygulama kimliği** girin.
    > [!div class="mx-imgBorder"]
    > ![WVAC profili oluşturmaya yönelik genel ayarlar bölümünü vurgulayan ekran görüntüsü.](media/custom-commands/create-profile.png)
1. **Kaydet ve profili Uygula '** yı seçin.
1. Şimdi konuşma/metin aracılığıyla aşağıdaki girişleri deneyin
    > [!div class="mx-imgBorder"]
    > ![WVAC profil oluştur](media/custom-commands/conversation.png)


> [!TIP]
> Özel komutlar hizmetinden gönderilen ham yanıtları incelemek için **etkinlik günlüğündeki** girişler ' e tıklayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, var olan bir uygulamayı kullandınız. Daha sonra, [nasıl yapılır bölümlerinde](./how-to-develop-custom-commands-application.md)özel bir komutlar uygulamasını sıfırdan tasarlamayı, geliştirmeyi, hatalarını ayıklamanızı, test yapmayı ve tümleştirmeyi öğreneceksiniz.
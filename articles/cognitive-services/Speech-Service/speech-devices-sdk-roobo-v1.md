---
title: Konuşma cihazları SDK Roobo akıllı ses geliştirme seti v1-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma cihazları SDK 'Sı ile çalışmaya başlama için Önkoşullar ve yönergeler, Roobo Smart Audio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 08bac7cd833f52d2dfec4561c2f87330a4119748
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552870"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Cihaz: Roobo akıllı ses geliştirme seti

Bu makalede, Roobo Smart Audio Dev Kit için cihaza özgü bilgiler sağlanmaktadır.

## <a name="set-up-the-development-kit"></a>Geliştirme Seti ' ayarlayın

1. Geliştirme setinde iki mikro USB bağlayıcısı vardır. Sol bağlayıcı, geliştirme setini güçlendirin ve aşağıdaki görüntüde güç olarak vurgulanır. Bu, doğru bir denetimdir ve görüntüde hata ayıklama olarak işaretlenir.

    ![dev Seti bağlanma](media/speech-devices-sdk/qsg-1.png)

1. Güç bağlantı noktasını bir BILGISAYARA veya güç bağdaştırıcısına bağlamak için mikro USB kablosu kullanarak geliştirme setini güçlendirin. Üst panonun altında yeşil bir güç göstergesi açılır.

1. Geliştirme setini denetlemek için, ikinci bir mikro USB kablosu kullanarak hata ayıklama bağlantı noktasını bir bilgisayara bağlayın. Güvenilir iletişimler sağlamak için yüksek kaliteli bir kablonun kullanılması önemlidir.

1. Uygulamanızı Geliştirme Seti için döngüsel ya da doğrusal yapılandırma yönlendirmek.

    |Geliştirme Seti yapılandırma|Hizalama|
    |-----------------------------|------------|
    |Döngüsel|Şekilde, tavan mikrofonlar ile yan yana|
    |Doğrusal|(Aşağıdaki görüntüde gösterilmiştir) alt tarafında, mikrofon ile karşılaşmış|

    ![Doğrusal dev Seti yönü](media/speech-devices-sdk/qsg-2.png)

1. Sertifikaları yükleyip ses cihazının izinlerini ayarlayın. Bir komut istemi penceresinde aşağıdaki komutları yazın:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Android hata ayıklama köprüsü bu komutları kullanmak `adb.exe`, Android Studio yüklemesinin bir parçası olduğu. Bu araç C:\Users bulunur\[kullanıcı adı] \AppData\Local\Android\Sdk\platform araçları. Bu dizin çağırmak daha kullanışlı hale getirmek için yola ekleyebilirsiniz `adb`. Aksi takdirde, yüklemenizin adb.exe çağıran her komut için tam yolunu belirtmeniz gerekir `adb`.
    >
    > Bir hata `no devices/emulators found` görürseniz, USB kablonuzun bağlı olduğunu ve yüksek kaliteli bir kablo olduğunu denetleyin. Bilgisayarınızın, cihaz `adb devices` listesini döndürdüğü için geliştirme seti ile konuştuğunu denetlemek için kullanabilirsiniz.
    >
    > [!TIP]
    > Bilgisayarınızın mikrofon ve Geliştirme Seti'nın mikrofonlar ile çalıştığından emin olmak Konuşmacı sessiz. Böylece, yanlışlıkla ses bilgisayardan cihazla tetiklemez.

1. Konuşmacı geliştirme setine eklemek istiyorsanız, ses satırına bağlanabilirsiniz. 3,5 mm analog bir eklentiyle iyi kalitede bir konuşmacı seçmeniz gerekir.

    ![Vysor ses](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Geliştirme bilgileri

Daha fazla geliştirme bilgisi için, [Roobo geliştirme kılavuzuna](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)bakın.

## <a name="audio"></a>Ses

Roobo, Flash belleğine tüm sesleri yakalayan bir araç sağlar. Ses sorunları gidermenize yardımcı olabilir. Aracı sürümü her Geliştirme Seti yapılandırması için sağlanır. [Roobo sitesinde](https://ddk.roobo.com/)cihazınızı seçin ve ardından sayfanın altındaki **roobo araçları** bağlantısını seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Android örnek uygulamasını çalıştırma](speech-devices-sdk-android-quickstart.md)

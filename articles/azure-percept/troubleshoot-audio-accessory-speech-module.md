---
title: Azure Percept Audio ve konuşma modülüyle ilgili sorunları giderme
description: Azure Percept Audio ve azureearspeechclientmodule için sorun giderme ipuçları alın
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605663"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept ses ve konuşma modülü sorunlarını giderme

Ses Yardımcısı uygulama sorunlarını gidermek için aşağıdaki yönergeleri kullanın.

## <a name="collecting-speech-module-logs"></a>Konuşma modülü günlüklerini toplama

Bu komutları çalıştırmak için, [Geliştirme Seti ' ne SSH ekleyin](./how-to-ssh-into-percept-dk.md) ve SSH istemci istemine komutları girin.

Konuşma modülü günlüklerini topla:

```console
sudo iotedge logs azureearspeechclientmodule
```

Daha fazla analiz için çıktıyı bir. txt dosyasına yeniden yönlendirmek için aşağıdaki sözdizimini kullanın:

```console
sudo [command] > [file name].txt
```

. Txt dosyasının kopyalanabilmesi için izinleri değiştirin:

```console
sudo chmod 666 [file name].txt
```

Çıktıyı bir. txt dosyasına yönlendirdikten sonra SCP aracılığıyla dosyayı ana bilgisayara kopyalayın:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[yerel ana bilgisayar dosyası yolu], ana bilgisayarınızdaki. txt dosyasını kopyalamak istediğiniz konumu ifade eder. [Uzak Kullanıcı adı], [kurulum deneyimi](./quickstart-percept-dk-set-up.md)SıRASıNDA seçilen SSH kullanıcı adıdır.

## <a name="checking-runtime-status-of-the-speech-module"></a>Konuşma modülünün çalışma zamanı durumu denetleniyor

**Azureearspeechclientmodule** çalışma zamanı durumunun **çalışır** durumda olup olmadığını denetleyin. Cihaz modüllerinizin çalışma zamanı durumunu bulmak için [Azure Portal](https://portal.azure.com/) açın ve **tüm kaynaklar**  ->  **[IoT Hub 'ınız]**  ->  **IoT Edge**  ->  **[cihazınızın kimliği]**' ne gidin. Tüm yüklü modüllerin çalışma zamanı durumunu görmek için **modüller** sekmesine tıklayın.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure portal uç cihaz sayfası.":::

**Azureearspeechclientmodule** çalışma zamanı durumu **çalışıyor** olarak listelenmiyorsa, **modülleri ayarla**  ->  **azureearspeechclientmodule**' ye tıklayın. **Modül ayarları** sayfasında, **istenen durumu** **çalışıyor** olarak ayarlayın ve **Güncelleştir**' e tıklayın.

## <a name="understanding-ear-som-led-indicators"></a>Ear SoM göstergelerini anlama

Cihazın hangi durumda olduğunu anlamak için LED göstergelerini kullanabilirsiniz. Genellikle modülün cihaz üzerindeki güçden sonra tam olarak başlatılması 2 dakika sürer. Başlatma adımlarında olduğu gibi şunları görürsünüz:

1. (Statik) Merkezi beyaz ışığı: cihaz açık.
2. Orta beyaz (yanıp sönen): kimlik doğrulama devam ediyor.
3. Cihaz kimlik doğrulamasından ve kullanıma hazırlandıktan sonra, üç LED 'in tümü mavi olarak değişir.

|GELIŞTIRMESINE|LED durumu|Ear SoM durumu|
|---|---------|--------------|
|L02|1x beyaz, statik açık|Açma |
|L02|1x beyaz, 0,5 Hz yanıp sönen|Kimlik doğrulama devam ediyor |
|L01 & L02 & L03|3x mavisi, statik|Anahtar sözcük bekleniyor|
|L01 & L02 & L03|LED dizisi yanıp sönme, 20 fps |Dinleme veya konuşuyor|
|L01 & L02 & L03|LED dizisi yarış, 20 fps|Düğün|
|L01 & L02 & L03|3x kırmızı, statik |Mikrofon|

## <a name="next-steps"></a>Sonraki adımlar

Azure Percept DK sorunlarını giderme hakkında daha fazla bilgi için bkz. [genel sorun giderme kılavuzu](./troubleshoot-dev-kit.md) .

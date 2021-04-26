---
title: Azure Percept ses cihazına genel bakış
description: Azure Percept ses hakkında daha fazla bilgi edinin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 23fae249cfceec75af0b7c49a3875ab447ecbafd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564271"
---
# <a name="introduction-to-azure-percept-audio"></a>Azure Percept ses 'e giriş

Azure Percept Audio, [Azure PERCEPT dk](./overview-azure-percept-dk.md)'ye konuşma AI özellikleri ekleyen bir aksesuar aygıtıdır. Önceden yapılandırılmış bir ses işlemcisi ve dört mikrofonla doğrusal dizi içerir ve Azure bilişsel hizmetler 'in yardımıyla sesli komutları, anahtar sözcük biriktirmelerini ve en fazla alanı konuşmayı kullanmanıza olanak sağlar. Azure Percept DK, [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)ve diğer Azure Edge Yönetim Hizmetleri ile tümleştirilmiştir. Azure Percept Audio, [Microsoft Online Mağazası](https://go.microsoft.com/fwlink/p/?LinkId=2155270)'nda satın alınabilir.

> [!div class="nextstepaction"]
> [Hemen satın alın](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Azure Percept ses bileşenleri

Azure Percept Audio aşağıdaki ana bileşenleri içerir:

- Üretim için, XMOS codec aracılığıyla dört mikrofonla bir doğrusal dizi ve ses işleme özellikli Azure Percept ses cihazı (SoM)
- Geliştirici (ınterposer) panosu: 2x düğmeler, 3x LED, mikro USB ve 3,5 mm ses jakı
- Gerekli kablolar: FPC kablosu, USB mikro tür-B-USB-A
- Hoş geldiniz kartı
- Tümleşik 80/20 1010 serisi bağlama ile mekanik bağlama plakası

## <a name="compute-capabilities"></a>İşlem özellikleri 

Azure Percept Audio, Azure Percept DK taşıyıcı panosunun CPU 'SU üzerinde bir karma uç bulutu halinde çalışan konuşma yığını aracılığıyla ses girişini geçirir. Bu nedenle, Azure Percept Audio, gerçekleştirmek için konuşma yığınını destekleyen bir işletim sistemi içeren bir taşıyıcı panosu gerektirir. 

Ses işleme şu şekilde yapılır: 

- Azure Percept Audio: sesi yakalar ve dönüştürür ve DK ve ses jakına gönderir.

- Azure Percept DK: konuşma yığını, işleme ve yankı iptali gerçekleştirir ve konuşmayı iyileştirmek için gelen sesi işler. İşlem tamamlandıktan sonra anahtar sözcük biriktirme uygular.

- Bulut: doğal dil komutlarını ve tümcecikleri, anahtar sözcük doğrulamasını ve yeniden eğitimi işler. 

- Çevrimdışı: cihaz çevrimdışıysa, anahtar sözcüğünü algılayacak ve internet bağlantısı durum telemetrisini yakalayacak. Bulutta anahtar sözcük doğrulaması gerçekleştirilemediği için anahtar sözcük biriktirme için artan bir yanlış kabul hızı gözlemlenebilir. 

## <a name="getting-started"></a>Kullanmaya başlama

- [Azure Percept DK 'yi birleştirin](./quickstart-percept-dk-unboxing.md)
- [Azure Percept ses cihazınızı devkit 'e bağlama](./quickstart-percept-audio-setup.md)
- [Azure Percept DK kurulum deneyimini doldurun](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>Kod içermeyen prototip oluşturma

Barındırma, sağlık, envanter ve oto senaryoları için Azure Percept Voice Yardımcısı şablonlarını kullanarak [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 'da [kod içermeyen bir konuşma çözümü](./tutorial-no-code-speech.md) oluşturun.

### <a name="manage-your-no-code-speech-solution"></a>Kod içermeyen konuşma çözümünüzü yönetin

- [Azure Percept Studio 'da sesli yardımcınızı yapılandırma](./how-to-manage-voice-assistant.md)
- [IoT Hub 'da sesli yardımcınızı yapılandırma](./how-to-configure-voice-assistant.md)
- [Azure Percept ses sorunlarını giderme](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Ek teknik bilgiler

- [Azure Percept ses veri sayfası](./azure-percept-audio-datasheet.md)
- [Düğme ve LED davranışı](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Microsoft Online Mağazası 'ndan bir Azure Percept ses cihazı satın alma](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

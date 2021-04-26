---
title: Konuşma cihazları SDK belgeleri
titleSuffix: Azure Cognitive Services
description: Sürüm notları, güncelleştirmelerin, geliştirmelerin, hata düzeltmelerinin ve konuşma cihazları SDK 'sında yapılan değişikliklerin günlüğünü sağlar. Bu makale, konuşma cihazları SDK 'sının her sürümüyle güncelleştirilir.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: f9215469b1436ce9dc95c140e6969366d80d1ced
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607142"
---
# <a name="release-notes-speech-devices-sdk"></a>Sürüm notları: konuşma cihazları SDK 'Sı

Aşağıdaki bölümlerde en son sürümlerde değişiklikler listelenmiştir.

## <a name="speech-devices-sdk-1150"></a>Konuşma cihazları SDK 1.15.0:

- Konuşma için geliştirilmiş beamoluşturan ve gürültü azaltmasıyla yeni Microsoft ses yığınına (MAS) yükseltilir.
- Hedefe bağlı olarak %70 ' a kadar ikili boyut azalır.
- [İkili sürüm](https://aka.ms/sdsdk-download-APAudio)Ile [Azure Percept ses](../../azure-percept/overview-azure-percept-audio.md) desteği.
- [Konuşma SDK](./speech-sdk.md) bileşeni 1.15.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.

## <a name="speech-devices-sdk-1110"></a>Konuşma cihazları SDK 1.11.0:

- [Rastgele mikrofon dizisi geometrileri](how-to-devices-microphone-array-configuration.md) ve bir [yapılandırma dosyası](https://aka.ms/sdsdk-micarray-json)aracılığıyla çalışma açısını ayarlama desteği.
- [Urdaha ıyı DDK](http://www.urbetter.com/products_56/278.html)desteği.
- [Ses Yardımcısı](https://aka.ms/sdsdk-speaker)örneğimizde kullanılan [GGEC konuşmacı](https://aka.ms/sdsdk-download-speaker) için ikili dosyalar yayınlandı.
- Raspberry Pi ve benzer cihazlarda Linux [ARM32](https://aka.ms/sdsdk-download-linux-arm32) ve [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) için ikili dosyalar yayınlandı.
- [Konuşma SDK](./speech-sdk.md) bileşeni 1.11.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.

## <a name="speech-devices-sdk-190"></a>Konuşma cihazları SDK 1.9.0:

- [Urdaha ıyı DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) için ilk ikililer sağlanır.
- Roobo v1 artık konuşma SDK 'Sı için Maven kullanıyor
- [Konuşma SDK](./speech-sdk.md) bileşeni 1.9.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.

## <a name="speech-devices-sdk-170"></a>Konuşma cihazları SDK 1.7.0:

- Linux ARM artık destekleniyor.
- [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) için ilk ikili dosyalar sağlanır (Linux ARM64).
- Windows kullanıcıları `AudioConfig.fromDefaultMicrophoneInput()` `AudioConfig.fromMicrophoneInput(deviceName)` Kullanılacak mikrofonu belirlemek için veya kullanabilir.
- Kitaplık boyutu iyileştirildi.
- Aynı konuşma/amaç tanıyıcı nesnesini kullanarak çok yönlü tanıma desteği.
- İşlem, tanıma durdurulduğunda işlemin yanıt vermemesine neden olan sorunu geçici olarak düzeltir.
- Örnek uygulamalar artık dosyanın biçimini göstermek için örnek katılımcılar. Properties dosyası içerir.
- [Konuşma SDK](./speech-sdk.md) bileşeni 1.7.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.

## <a name="speech-devices-sdk-160"></a>Konuşma cihazları SDK 1.6.0:

- Ortak [örnek uygulamayla](./speech-devices-sdk.md) Windows ve Linux 'Ta [Azure Kinect dk](https://azure.microsoft.com/services/kinect-dk/) 'yi destekleme
- [Konuşma SDK](./speech-sdk.md) bileşeni 1.6.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.

## <a name="speech-devices-sdk-151"></a>Konuşma cihazları SDK 1.5.1:

- Örnek uygulamada [konuşma dökümünü](./conversation-transcription.md) dahil edin.
- [Konuşma SDK](./speech-sdk.md) bileşeni 1.5.1 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.

## <a name="speech-devices-sdk-150-2019-may-release"></a>Konuşma cihazları SDK 1.5.0:2019-yayın Mayıs

- Konuşma cihazları SDK 'Sı artık GA ve artık geçişli bir önizleme değildir.
- [Konuşma SDK](./speech-sdk.md) bileşeni 1.5.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.
- Yeni anahtar sözcük teknolojisi önemli kalite iyileştirmeleri getirir, bkz. son değişiklikler.
- Geliştirilmiş en fazla alan tanıma için yeni ses işleme işlem hattı.

**Yeni değişiklikler**

- Yeni anahtar sözcük teknolojisi nedeniyle, geliştirilmiş anahtar sözcük portalımızda tüm anahtar sözcüklerin yeniden oluşturulması gerekir. Eski anahtar sözcükleri cihazdan tamamen kaldırmak için eski uygulamayı kaldırın.
  - ADB com. Microsoft. biliveservices. Speech. Samples. sdsdkstarterapp kaldırma

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Konuşma cihazları SDK 1.4.0:2019-Nis sürümü

- [Konuşma SDK](./speech-sdk.md) bileşeni 1.4.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Konuşma cihazları SDK 1.3.1:2019-Mar sürümü

- [Konuşma SDK](./speech-sdk.md) bileşeni 1.3.1 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.
- Anahtar sözcük işleme güncelleştirildi, bkz. son değişiklikler.
- Örnek uygulama, konuşma tanıma ve çeviri için dil seçimi ekler.

**Yeni değişiklikler**

- [Anahtar sözcük yükleme](./custom-keyword-basics.md) basitleştirildi, artık uygulamanın bir parçasıdır ve cihaza ayrı yükleme gerektirmez.
- Anahtar sözcük tanıma değiştirilmiştir ve iki olay desteklenir.
  - `RecognizingKeyword,` konuşma sonucu 'nın (doğrulanmamış) anahtar sözcük metnini içerdiğini belirtir.
  - `RecognizedKeyword`, anahtar sözcük tanımanın verilen anahtar sözcüğü tanımayı tamamladığını gösterir.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Konuşma cihazları SDK 1.1.0:2018-kas sürümü

- [Konuşma SDK](./speech-sdk.md) bileşeni 1.1.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.
- En fazla alan konuşma tanıma doğruluğu, gelişmiş ses işleme algoritmamıza göre geliştirildi.
- Örnek uygulama, Çince konuşma tanıma desteği ekledi.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Konuşma cihazları SDK 1.0.1:2018-eki açılan sürümü

- [Konuşma SDK](./speech-sdk.md) bileşeni 1.0.1 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](./releasenotes.md)bakın.
- Konuşma tanıma doğruluğu, geliştirilmiş ses işleme algoritmamıza göre iyileştirilen
- Tek bir sürekli tanıma ses oturumu hatası düzeltildi.

**Yeni değişiklikler**

- Bu sürümde, bir dizi Son değişiklik yapılmıştır. API 'Lerle ilgili ayrıntılar için lütfen [Bu sayfayı](https://aka.ms/csspeech/breakingchanges_1_0_0) kontrol edin.
- Anahtar sözcük tanıma modeli dosyaları, konuşma cihazları SDK 1.0.1 ile uyumlu değildir. Yeni anahtar sözcük dosyaları cihaza yazıldıktan sonra var olan anahtar sözcük dosyaları silinir.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Konuşma cihazları SDK 0.5.0:2018-Ağu yayını

- Ses işleme kodundaki bir hatayı düzelterek konuşma tanımanın doğruluğu geliştirilmiştir.
- [Konuşma SDK](./speech-sdk.md) bileşeni 0.5.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)bakın.

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Konuşma cihazları SDK 0.2.12733:2018-yayın Mayıs

Bilişsel hizmetler konuşma cihazları SDK 'sının ilk genel önizleme sürümü.
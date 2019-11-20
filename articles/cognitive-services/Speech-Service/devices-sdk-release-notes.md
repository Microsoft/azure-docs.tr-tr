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
ms.date: 11/13/2019
ms.author: wellsi
ms.openlocfilehash: 3a74f3eb9aece3535f5505d69833a074c2dd0ed2
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091192"
---
# <a name="release-notes-speech-devices-sdk"></a>Sürüm notları: konuşma cihazları SDK 'Sı

Aşağıdaki bölümlerde en son sürümlerde değişiklikler listelenmiştir.

## <a name="speech-devices-sdk-170"></a>Konuşma cihazları SDK 1.7.0:

*   Linux ARM artık destekleniyor.
*   Roobov2 için ilk ikililer sağlanır (Linux ARM64).
*   Windows kullanıcıları, kullanılacak mikrofonu belirtmek için AudioConfig. Fromdefaultmikro Phoneınput () veya AudioConfig. Frommikro Phoneınput (aygıtadı) kullanabilir.
*   Kitaplık boyutu iyileştirildi.
*   Aynı konuşma/amaç tanıyıcı nesnesini kullanarak çok yönlü tanıma desteği.
*   Tanımayı durdururken ortaya çıkabilecek zaman zaman asılı kalma özelliğini düzeltir.
*   Örnek uygulamalar artık dosyanın biçimini göstermek için örnek katılımcılar. Properties dosyası içerir.
*   [Konuşma SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bileşeni 1.7.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](https://aka.ms/csspeech/whatsnew)bakın.

## <a name="speech-devices-sdk-160"></a>Konuşma cihazları SDK 1.6.0:

- Ortak [örnek uygulamayla](https://aka.ms/sdsdk-download) Windows ve Linux 'Ta [Azure Kinect dk](https://azure.microsoft.com/services/kinect-dk/) 'yi destekleme
- [Konuşma SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bileşeni 1.6.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](https://aka.ms/csspeech/whatsnew)bakın.

## <a name="speech-devices-sdk-151"></a>Konuşma cihazları SDK 1.5.1:

- Örnek uygulamada [konuşma dökümünü](conversation-transcription-service.md) dahil edin.
- [Konuşma SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bileşeni 1.5.1 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](https://aka.ms/csspeech/whatsnew)bakın.

## <a name="speech-devices-sdk-150-2019-may-release"></a>Konuşma cihazları SDK 1.5.0:2019-yayın Mayıs

- Konuşma cihazları SDK 'Sı artık GA ve artık geçişli bir önizleme değildir.
- [Konuşma SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bileşeni 1.5.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](https://aka.ms/csspeech/whatsnew)bakın.
- Yeni anahtar sözcük teknolojisi önemli kalite iyileştirmeleri getirir, bkz. son değişiklikler.
- Geliştirilmiş en fazla alan tanıma için yeni ses işleme işlem hattı.

**Bozucu değişiklikler**

- Yeni anahtar sözcük teknolojisi nedeniyle, geliştirilmiş anahtar sözcük portalımızda tüm anahtar sözcüklerin yeniden oluşturulması gerekir. Eski anahtar sözcükleri cihazdan tamamen kaldırmak için eski uygulamayı kaldırın.
  - ADB com. Microsoft. cogınitiveservices. Speech. Samples. sdsdkstarterapp 'i kaldır

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Konuşma cihazları SDK 1.4.0:2019-Nis sürümü

- [Konuşma SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bileşeni 1.4.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](https://aka.ms/csspeech/whatsnew)bakın.

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Konuşma cihazları SDK 1.3.1:2019-Mar sürümü

- [Konuşma SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bileşeni 1.3.1 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](https://aka.ms/csspeech/whatsnew)bakın.
- Anahtar sözcük işleme güncelleştirildi, bkz. son değişiklikler.
- Örnek uygulama, konuşma tanıma ve çeviri için dil seçimi ekler.

**Bozucu değişiklikler**

- [Anahtar sözcük yükleme](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) basitleştirildi, artık uygulamanın bir parçasıdır ve cihaza ayrı yükleme gerektirmez.
- Anahtar sözcük tanıma değiştirilmiştir ve iki olay desteklenir.
  - Recognizinganahtar sözcüğü, konuşma sonucu içeren (doğrulanmamış) anahtar sözcük metnini gösterir.
  - Recognizedanahtar sözcüğü, anahtar sözcük tanımanın verilen anahtar sözcüğü tanımayı tamamladığını gösterir.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Konuşma cihazları SDK 1.1.0:2018-kas sürümü

- [Konuşma SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bileşeni 1.1.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](https://aka.ms/csspeech/whatsnew)bakın.
- En fazla alan konuşma tanıma doğruluğu, gelişmiş ses işleme algoritmamıza göre geliştirildi.
- Örnek uygulama, Çince konuşma tanıma desteği ekledi.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Konuşma cihazları SDK 1.0.1:2018-eki açılan sürümü

- [Konuşma SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bileşeni 1.0.1 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](https://aka.ms/csspeech/whatsnew)bakın.
- Konuşma tanıma doğruluğu, geliştirilmiş ses işleme algoritmamıza göre iyileştirilen
- Tek bir sürekli tanıma ses oturumu hatası düzeltildi.

**Bozucu değişiklikler**

- Bu sürümle birlikte birkaç önemli değişiklikler yapılmıştır. API 'Lerle ilgili ayrıntılar için lütfen [Bu sayfayı](https://aka.ms/csspeech/breakingchanges_1_0_0) kontrol edin.
- KWS model dosyaları, konuşma cihazları SDK 1.0.1 ile uyumlu değildir. Yeni anahtar sözcük dosyaları cihaza yazıldıktan sonra var olan anahtar sözcük dosyaları silinir.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Konuşma cihazları SDK 0.5.0:2018-Ağu yayını

- Ses işleme kodundaki bir hatayı düzelterek konuşma tanımanın doğruluğu geliştirilmiştir.
- [Konuşma SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bileşeni 0.5.0 sürümüne güncelleştirildi. Daha fazla bilgi için [sürüm notlarına](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)bakın.

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Konuşma cihazları SDK 0.2.12733:2018-yayın Mayıs

Bilişsel hizmetler konuşma cihazları SDK 'sının ilk genel önizleme sürümü.

---
title: Azure Media Player özellik listesi
description: Azure Media Player için bir özellik başvurusu.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 4b0666b439c284fd402b3f6e04bbaed14d6aa358
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448791"
---
# <a name="feature-list"></a>Özellik listesi #
Sınanan özelliklerin ve desteklenmeyen özelliklerin listesi aşağıda verilmiştir:

| Özellik | K | KıSMEN TEST EDILDI | TEST EDILMEMIŞ | Desteklenen | NOTLAR |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| **Kayıttan yürütme**                                |        |                  |          |             |                                                                                                                      |
| Temel Isteğe bağlı kayıttan yürütme                | X      |                  |          |             | Yalnızca Azure Media Services akışlarını destekler                                                                      |
| Temel canlı kayıttan yürütme                     | X      |                  |          |             | Yalnızca Azure Media Services akışlarını destekler                                                                      |
| AES                                     | X      |                  |          |             | Azure Media Services anahtar teslim hizmetini destekler                                                                   |
| Çoklu DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Azure Media Services anahtar teslim hizmetini destekler                                                                   |
| Widevine                                |        | X                |          |             | Bildirimde özetlenen Wıdevine PSSH kutularını destekler                                                                    |
| FairPlay                                |        | X                |          |             | Azure Media Services anahtar teslim hizmetini destekler                                                                   |
| **Techs**                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash geri dönüş (FlashSS)                | X      |                  |          |             | Bu teknik üzerinde tüm özellikler kullanılamaz.                                                                         |
| Silverlight geri dönüş SilverlightSS      | X      |                  |          |             | Bu teknik üzerinde tüm özellikler kullanılamaz.                                                                         |
| Yerel HLS geçiş (HTML5)         |        | X                |          |             | Platform kısıtlamaları nedeniyle bu teknik üzerinde tüm özellikler kullanılamaz.                                            |
| **Özellikler**                                |        |                  |          |             |                                                                                                                      |
| API desteği                             | X      |                  |          |             | Bilinen sorunlar listesine bakın                                                                                                |
| Temel Kullanıcı arabirimi                                | X      |                  |          |                                                                                                                                    |
| JavaScript üzerinden başlatma       | X      |                  |          |             |                                                                                                                      |
| Video etiketi aracılığıyla başlatma        |        | X                |          |             |                                                                                                                      |
| Kesim adresleme-zaman tabanlı         | X      |                  |          |             |                                                                                                                      |
| Kesim adresleme-dizin tabanlı        |        |                  |          | X           |                                                                                                                      |
| Kesim adresleme-bayt tabanlı         |        |                  |          | X           |                                                                                                                      |
| Azure Media Services URL yeniden yazıcı       |        | X                |          |             |                                                                                                                      |
| Erişilebilirlik-açıklamalı alt yazılar ve alt yazılar  | X      |                 |          |             |  WebVTT (isteğe bağlı), CEA 708 (isteğe bağlı ve canlı) ve IMSC1 (isteğe bağlı ve canlı)                                                       |
| Erişilebilirlik-kısayol tuşları                 | X      |                  |          |             |                                                                                                                      |
| Erişilebilirlik-Yüksek Karşıtlık           |        | X                |          |             |                                                                                                                      |
| Erişilebilirlik-sekme odaklı               |        | X                |          |             |                                                                                                                      |
| Hata Iletisi                         |        | X                |          |             | Techs genelinde hata iletileri tutarsız                                                                         |
| Olay tetikleme                        | X      |                  |          |             |                                                                                                                      |
| Tanılama                             |        | X                |          |             | Tanılama bilgileri yalnızca AzureHtml5JS Tech ve SilverlightSS Tech üzerinde kısmen kullanılabilir. |
| Özelleştirilebilir teknoloji siparişi                 |        | X                |          |             |                                                                                                                      |
| Buluşsal yöntemler-temel                      | X      |                  |          |             |                                                                                                                      |
| Buluşsal yöntemler-özelleştirme              |        |                  | X        |             | Özelleştirme yalnızca AzureHtml5JS Tech ile kullanılabilir.                                                          |
| Süreksizlik                         | X      |                  |          |             |                                                                                                                      |
| Bit hızı seçin                          | X      |                  |          |             | Bu API yalnızca AzureHtml5JS ve FlashSS Techs üzerinde kullanılabilir.                                                    |
| Çoklu ses akışı                      |        | X                |          |             | Programlı ses anahtarı AzureHtml5JS ve FlashSS Techs 'de desteklenir ve AzureHtml5JS, FlashSS ve Native HTML5 (Safari 'de) üzerinde kullanıcı arabirimi seçimi aracılığıyla kullanılabilir.  Çoğu platform, ses akışlarını (aynı codec, kanal, örnekleme oranı vb.) değiştirmek için aynı codec özel verilerini gerektirir. |
| UI yerelleştirme                         |        | X                |          |             |                                                                                                                      |
| Çok örnekli yürütme                 |        |                  |          | X           | Bu senaryo bazı Techs için çalışabilir, ancak şu anda desteklenmeyen ve test edilmemiş. Bunu iframe 'leri kullanarak da işe alabilirsiniz |
| Ads desteği                             |        | X                |          |             | AMP, AzureHtml5JS Tech 'de VOD için büyük ölçekli ad sunucularından orta ve sonrası doğrusal reklamların eklenmesini destekler |
| Analiz                               |        | X                |          |             | AMP, istediğiniz analiz arka ucunu göndermek için analiz ve Tanılama olaylarını dinlemek için olanak sağlar.  Platform sınırlamaları nedeniyle Techs genelinde tüm olaylar ve özellikler kullanılamaz.                                                                            |
| Özel kaplamalar                            |        |                  | X        |             | Bu senaryo, AMP 'daki false değerine ayarlanarak ve kendi HTML ve CSS 'niz kullanılırken elde edilebilir.           |
| Arama çubuğu Temizleme                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Yalnızca ses                              | X      |                  |          |           | AzureHtml5JS içinde desteklenir. İlerleyen MP3 kayıttan yürütme, platform destekliyorsa HTML5 teknik ile çalışabilir.                                                                                                        |
| Yalnızca video                              | X      |                  |          |           | AzureHtml5JS içinde desteklenir.                                                                                                        |
| Çok dönemdeki sunum               |        |                  |          | X                                                                                                                                  |
| Birden çok kamera açısı                  |        |                  |          | X           |                                                                                                                      |
| Kayıttan yürütme hızı                          |        | X                |          |             | Kayıttan yürütme hızı, Chrome 'daki kısmi bir hatadan dolayı Mobil durum dışında çoğu senaryoda desteklenir                 |

## <a name="next-steps"></a>Sonraki adımlar ##
- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)
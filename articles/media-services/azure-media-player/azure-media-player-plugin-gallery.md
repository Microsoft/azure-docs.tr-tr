---
title: Azure Media Player eklentisi Galerisi
description: Bu makale Azure Media Player için kullanılabilen eklentilerin bir listesini içerir.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: d958d93731f38b4b553bca94f48c740c1b75cfd5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449860"
---
# <a name="azure-media-player-plugin-gallery"></a>Azure Media Player eklentisi Galerisi #

## <a name="plugins"></a>Eklentiler ##

| Eklenti adı                         | Tanıtım URL 'SI                    | Kaynak Kodu                | Açıklama    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Ek özellikler                 | | | |
| **Yeni!** AMP360Video                | [Tanıtım](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Eklenti, masaüstünüzde veya VR uyumlu cihazlarda amp 'da 360 videosunu görselleştirmenize olanak tanır. Tam belgelere [buradan](https://doc.babylonjs.com/extensions/amp360video)ulaşabilirsiniz: |
|  Sprite Ipucu                         | [Tanıtım](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Azure Media Services (AMS) Media Encoder Standard (MES) tarafından oluşturulan bir video küçük resim görüntüsü için zaman çizelgesi işlemesi için Azure Media Player (AMP) eklentisi. |
| Tanılama yer paylaşımı                 | [Tanıtım](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Bu eklenti şunu görüntüler: tüm anahtar parametreleri, video istatistikleri, video kayıttan yürütme yaşam döngüsünün tüm olayları ve korunuyorsa anahtar KIMLIĞI, lisans alma URL 'Leri gibi DRM koruma bilgileri.                                                                                                                                                                      |
| Kare hızı ve timecode Hesaplayıcı | Tanıtım yok | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Bu eklenti, `tfhd` / `trun` ilk MPEG-Dash video parçasının MP4 kutularına göre videonun kare hızını hesaplar, MPEG-Dash istemci bildiriminden zaman ölçeği değerini ayrıştırır ve ayrıca, Player 'dan verilen mutlak bir süre için timecode oluşturma ve aynı zamanda Player 'ın zaman koduna verilen mutlak zamanı sağlayan bir yol sağlar. |
| <strike>Kayıttan yürütme hızı</strike>                      | [Tanıtım](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Bu eklenti, görüntüleyicilerin video hızını denetlemesine olanak sağlar. *Bu işlev, AMP v 2.0.0 + sürümünde otomatik olarak kullanılabilir ancak varsayılan olarak devre dışı bırakılmıştır.* Nasıl etkinleştireceğinizi öğrenmek için [buradaki](https://github.com/Azure-Samples/azure-media-player-samples) örneklere göz atın |
| Vurgulama süresi Ipucu                      | [Tanıtım](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Zamana doğru arama için fare üzerine gelindiğinde ilerleme çubuğunun üzerinde bir zaman ipucu görüntüler. *Note: Bu eklenti, amp ile zaten tümleşiktir* ancak nasıl programlandığını görmek istiyorsanız, bir göz atın.                                                                                                                 |
| Başlık kaplama                       | [Tanıtım](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Yapılandırılabilir video başlığını ekran üzerinde yer paylaşımı |
| Zaman çizelgesi Işaretçileri                    | [Tanıtım](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Bu eklenti, bir dizi zaman alır ve bu zamanlarda ilerleme çubuğunun üzerinde küçük işaretçiler kaplar. |
| Analiz                           | | | |
| Application Insights                | [Blog gönderisi](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Player ölçümlerinizi izleyen ve görüntüleyicilerin oynatıcı deneyiminin sezgisel bir grafik gösterimi için Power BI bağlantı noktalarından oluşan eklenti. |
| Google Analytics                    | Yok                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Azure Media Player için Google Analytics eklentisi |
| Tanılama                         | | | |
| Tanılama çıkışı                  | [Tanıtım](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Bu eklenti, oynatıcınızın bir dizi tanılamayı çıktı ve bu işlemin nasıl çalıştığını görmek için tanıtım bağlantısına gidin ve JavaScript konsolunuzu açın. |
| Erişim Kolaylığı                      | | | |
| Yakınlaştır                             | [Tanıtım](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Bu eklenti, görüntüleyicilerin içeriğinizi yakınlaşabilmesi için oyuncular ekranında sürükle özellikli yakınlaştırma ölçeği görüntüler |
| Canlı açıklamalı alt yazılar                       | [Azure blog gönderisi](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[subply Post](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | Yok | *Daha fazla bilgi için gönderi bölümüne bakın.* Canlı açıklamalı altyazı için tasarlanan uçtan uca iş akışı Azure Media Player için, alt sayfanın sitesine gitmek ve çözüm hakkında daha fazla bilgi edinmek için en soldaki bağlantıya tıklayın |
| Sık kullanılan tuşlar                            | <strike>[Tanıtım](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Sık kullanılan anahtarlar eklentisi, görüntüleyicilerin, ekran için F, sessiz ve ilerleme çubuğu denetimi için ok tuşları gibi genel eklenti birleşimleri ile Player 'ın çeşitli yönlerini denetlemesine olanak sağlar. *Note: Bu eklenti zaten AMP ile tümleştirildi, ancak bunu kaynak olarak kullanma ücretsizdir* |
| Sosyal                              | | | |
| Paylaş                               | [Tanıtım](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Bu eklenti, görüntüleyicilerinizin Facebook, Twitter veya LinkedIn aracılığıyla arkadaşlarınızla yaptıkları videoyu paylaşabilmesi için Player 'ın denetim çubuğuna bir Paylaş düğmesi ekler. |

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)

---
title: Azure Kinect Görüntüleyicisi
description: Azure Kinect görüntüleyicisini kullanarak tüm cihaz veri akışlarını görselleştirmeyi anlayın.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, algılayıcı, Görüntüleyici, görselleştirme, derinlik, RGB, renk, İmu, ses, mikrofon, nokta bulutu
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92166114"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect Görüntüleyicisi

Yüklü araçlar dizini altında bulunan Azure Kinect Viewer `k4aviewer.exe` (örneğin, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` `X.Y.Z` SDK 'nın yüklü sürümü), tüm cihaz veri akışlarını görselleştirmek için kullanılabilir:

* Sensörların doğru şekilde çalıştığını doğrulayın.
* Cihazı konumlandırmaya yardımcı olun.
* Kamera ayarları ile denemeler yapın.
* Cihaz yapılandırmasını okuyun.
* [Azure Kinect Kaydedicisi](azure-kinect-recorder.md)ile yapılan kayıttan yürütme kayıtları.

Azure Kinect Görüntüleyicisi hakkında daha fazla bilgi için [Azure Kinect videosunu nasıl kullanacağınızı](https://www.microsoft.com/videoplayer/embed/RE3hNwG)izleyin.

Azure Kinect Görüntüleyicisi [açık kaynaktır](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) ve API 'lerin kullanımına ilişkin bir örnek olarak kullanılabilir.

## <a name="use-viewer"></a>Görüntüleyiciyi kullanma

Görüntüleyici iki modda çalışabilir: sensörden veya kayıtlı verilerden ([Azure Kinect Kaydedicisi](azure-kinect-recorder.md)) canlı verilerle birlikte.

### <a name="start-application"></a>Uygulamayı Başlat

Çalıştırarak uygulamayı başlatın `k4aviewer.exe` .

![Başlatılan görüntüleyici uygulamasını gösteren ekran görüntüsü.](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>Canlı verilerle görüntüleyiciyi kullanma

1. **Cihazı aç** bölümünde, açılacak cihazın **seri numarasını** seçin. Ardından, cihaz eksikse **Yenile**' yi seçin.
2. **Cihazı aç** düğmesini seçin.
3. Varsayılan ayarlarla veri akışı başlatmak için **Başlat** ' ı seçin.

### <a name="use-the-viewer-with-recorded-data"></a>Kaydedilen verilerle görüntüleyiciyi kullanma

**Kayıt aç** bölümünde, kaydedilen dosyaya gidin ve dosyayı seçin.

## <a name="check-device-firmware-version"></a>Cihaz üretici yazılımı sürümünü denetleme

Aşağıdaki görüntüde gösterildiği gibi, yapılandırma penceresinde cihaz üretici yazılımı sürümüne erişin.

![Görüntüleyici kullanarak cihaz üretici yazılımı sürümünü denetleme](./media/how-to-guides/check-firmware-update.png)

Örneğin, bu durumda, derinlik kamera ISS 'si, ILT 1.5.63 çalıştırıyorsa.

## <a name="depth-camera"></a>Derinlik kamerası

Derinlik kamera görüntüleyicisinde iki pencere görüntülenir:

* Biri, IR parlaklığını gösteren gri tonlamalı bir görüntü olan *etkin parlaklık* olarak adlandırılır.
* İkincisi, derinlik verilerinin renklendirilmiş bir gösterimine sahip olan *derinlik* olarak adlandırılır.

Aşağıda gösterildiği gibi, derinlik algılayıcısı değerini görmek için imlecinizi derinlik penceresindeki pikselde gezdirin.

![Derinlik görünümü](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>RGB Kamerası

Aşağıdaki görüntüde renkli kamera görünümü gösterilmektedir.

![RGB görünümü](./media/how-to-guides/viewer-rgb-camera.png)

Akış sırasında yapılandırma penceresinden RGB kamera ayarlarını denetleyebilirsiniz.

![RGB kamera denetimleri](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>Inertial ölçü birimi (ıMU)

IMU penceresinin iki bileşeni, bir ivme ölçer ve bir cayroscope vardır.

En üstteki, ivme ölçer ve ölçer/saniye<sup>2</sup>cinsinden doğrusal hızlandırma gösterir.  Bu, yerçekimi hızlandırmayı içerir, bu nedenle bir tabloda yer alıyorsa Z ekseni büyük olasılıkla 9,8 m/s<sup>2</sup>etrafında görünür.

Alt yarım, cayroscope bölümüdür ve radyan/saniye cinsinden rotasyonlu hareketi gösterir

![Hareket algılayıcısı görünümü](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>Mikrofon girişi

Mikrofon görünümü her bir mikrofona sesli duyun bir temsilini gösterir. Ses yoksa, grafik boş olarak gösterilir; Aksi takdirde, üzerine açık mavi bir dalga işareti içeren koyu mavi bir dalga işareti görürsünüz.

Koyu dalga, bu zaman dilimi üzerindeki mikrofondan gözlenen en düşük ve en yüksek değerleri temsil eder. Hafif dalga, bu zaman dilimi üzerinden mikrofonla gözlenen değerlerin kök ortalama karesini temsil eder.

![Mikrofon giriş görünümü](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Nokta bulutu görselleştirmesi

3D olarak görselleştirilen derinlik, talimat verilen tuşları kullanarak görüntüye geçiş yapmanızı sağlar.

![Derinlik noktası bulutu](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>Eşitleme denetimi

Birden çok cihazlı eşitlemeyi yapılandırırken cihazı tek başına (varsayılan), ana veya alt mod olarak yapılandırmak için görüntüleyiciyi kullanabilirsiniz.
Yapılandırma değiştirme veya eşitleme kablosu ekleme/kaldırma sırasında, güncelleştirmek için **Yenile** ' yi seçin.

![Dış eşitleme denetimi](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Dış eşitleme Kurulum Kılavuzu](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)

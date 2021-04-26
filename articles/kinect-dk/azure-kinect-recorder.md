---
title: Azure Kinect DK Kaydedicisi
description: Azure Kinect kaydedicisini kullanarak, algılayıcı SDK 'sından veri akışlarını bir dosyaya nasıl kaydedebileceğinizi anlayın.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, kayıt, kayıttan yürütme, okuyucu, Matroska, MKV, akışlar, derinlik, RGB, kamera, renk, İmu, ses
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277671"
---
# <a name="azure-kinect-dk-recorder"></a>Azure Kinect DK Kaydedicisi

Bu makalede, `k4arecorder` ALGıLAYıCı SDK 'sindeki veri akışlarını bir dosyaya kaydetmek için komut satırı yardımcı programını nasıl kullanabileceğiniz ele alınmaktadır.

>[!NOTE]
>Azure Kinect kaydedicisi ses kaydetmez.

## <a name="recorder-options"></a>Kaydedici seçenekleri

, `k4arecorder` Çıkış dosyası ve kayıt modlarını belirtmek için çeşitli komut satırı bağımsız değişkenlerine sahiptir.

Kayıtlar [Matroska. MKV biçiminde](record-file-format.md)depolanır. Kayıt, renk ve derinlik için birden çok video parçasını ve ayrıca kamera ayarlama ve meta verileri gibi ek bilgileri kullanır.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>Kayıt dosyaları

Örnek 1. Kayıt derinliği NFOV (640x576) modu, RGB 1080p, ıMU ile 30 fps üzerinde.
Kaydı durdurmak için **CTRL-C** tuşlarına basın.

```
k4arecorder.exe output.mkv
```

Örnek 2. WFOV kümelenmemiş (1MP), RGB 3072p 'yi ıMU olmadan on saniye boyunca 15 fps 'ye kaydedin.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

Örnek 3. WFOV 2x2 ' yi 5 saniye boyunca 30 fps 'e kaydeder ve çıktıyı. mkv dosyasına kaydeder.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>[Azure Kinect görüntüleyicisini](azure-kinect-viewer.md) , KAYDETMEDEN önce RGB kamera denetimlerini yapılandırmak için kullanabilirsiniz (örneğin, el ile beyaz denge ayarlamak için).

## <a name="verify-recording"></a>Kaydı doğrula

Output. mkv dosyasını [Azure Kinect Görüntüleyicisi](azure-kinect-viewer.md)ile açabilirsiniz.

Parçaları ayıklamak veya dosya bilgilerini görüntülemek için, gibi araçlar, `mkvinfo` [MKVToolNix](https://mkvtoolnix.download/) araç setinin bir parçası olarak kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

[Kaydedici 'yi dış eşitlenmiş birimlerle kullanma](record-external-synchronized-units.md)
---
title: Dosya biçimini kaydetmek için Azure Kinect algılayıcı SDK 'sını kullanma
description: Azure Kinect algılayıcı SDK 'Sı kayıtlı dosya biçiminin nasıl kullanılacağını anlayın.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: Kinect, Azure, algılayıcı, SDK, derinlik, RGB, kayıt, kayıttan yürütme, Matroska, MKV
ms.openlocfilehash: f4fa14b0841cb76b2ba191310ecbca312d29f805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97654600"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>Dosya biçimini kaydetmek için Azure Kinect algılayıcı SDK 'sını kullanma

Algılayıcı verilerini kaydetmek için, birden çok izlemelerin çok sayıda codec kullanılarak depolanmasını sağlayan Matroska (. MKV) kapsayıcı biçimi kullanılır. Kayıt dosyası renk, derinlik, IR görüntüleri ve ıMU depolama için izler içerir.

. MKV kapsayıcı biçiminin alt düzey ayrıntıları [Matroska Web sitesinde](https://www.matroska.org/index.html)bulunabilir.

| Adı izle | Codec biçim                          |
|------------|---------------------------------------|
| Renk      | Mode-Dependent (MJPEG, NV12 veya YUY2) |
| DERINLIĞINI      | b16g (16 bit gri tonlamalı, büyük endian)   |
| IR         | b16g (16 bit gri tonlamalı, büyük endian)   |
| IMU        | Özel yapı, aşağıdaki [IMU örnek yapısına](record-file-format.md#imu-sample-structure) bakın. |

## <a name="using-third-party-tools"></a>Üçüncü taraf araçları kullanma

`ffmpeg` `mkvinfo` [MKVToolNix](https://mkvtoolnix.download/) araç setindeki veya komutu gibi araçlar, kayıt dosyalarından bilgi görüntülemek ve ayıklamak için kullanılabilir.

Örneğin, aşağıdaki komut, derinlik izlemeyi aynı klasöre 16 bit PNG sırası olarak ayıklar:

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1`Bu parametre, çoğu kayıt için derinlik olacak şekilde izleme dizini 1 ' i ayıklar. Kayıt bir renk izlemesi içermiyorsa, `-map 0:0` kullanılır.

`-vsync 0`Parametresi, korumalı kare sayısını 30 fps, 15 fps veya 5 fps ile eşleştirmek yerine olduğu gibi çerçeveleri ayıklamak için FFmpeg 'yi zorlar.

## <a name="imu-sample-structure"></a>IMU örnek yapısı

IMU verileri kayıttan yürütme API 'SI kullanılmadan ayıklandığında, veriler ikili biçimde olur.
IMU verilerinin yapısı aşağıda verilmiştir. Tüm alanlar az endian.

| Alan                        | Tür     |
|------------------------------|----------|
| İvme ölçer zaman damgası (μs) | Int64   |
| Hızlandırma verileri (x, y, z) | float [3] |
| Cayroscope zaman damgası (μs)     | Int64   |
| Jroscope verileri (x, y, z)     | float [3] |

## <a name="identifying-tracks"></a>Parçaları tanımlama

Hangi izlemenin renk, derinlik, IR vb. içerdiğini belirlemek gerekebilir. Matroska bir dosyayı okumak için üçüncü taraf araçlarla çalışırken, izlemelerin tanımlanması gerekir.
Sayıları izlemek, kamera moduna ve etkinleştirilmiş parçaların kümesine göre farklılık gösterir. Etiketler her bir izlemenin anlamını belirlemek için kullanılır.

Aşağıdaki etiketlerin listesi, her biri belirli bir Matroska öğesine iliştirilir ve ilgili izlemeyi ya da eki aramak için kullanılabilir.

Bu Etiketler ve gibi araçlarla görüntülenebilir `ffmpeg` `mkvinfo` .
Etiketlerin tam listesi, [kayıt ve kayıttan yürütme](record-playback-api.md) sayfasında listelenir.

| Etiket Adı             | Etiket hedefi             | Etiket değeri             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | Renk Izi            | Matroska Track UID    |
| K4A_DEPTH_TRACK      | Derinlik Izi            | Matroska Track UID    |
| K4A_IR_TRACK         | IR Izleme               | Matroska Track UID    |
| K4A_IMU_TRACK        | IMU Izi              | Matroska Track UID    |
| K4A_CALIBRATION_FILE | Ayarlama Eki | Ek dosya adı   |

## <a name="next-steps"></a>Sonraki adımlar

[Kayıt ve kayıttan yürütme](record-playback-api.md)
